---
title: Microsoft Azure dados encriptação em repouso | Documentos da Microsoft
description: Este artigo fornece uma visão geral do Microsoft Azure encriptação inativa de dados, as capacidades globais e as considerações gerais.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 4ced712b1b2716d85f0366ea892460053db598b8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076909"
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data Encryption-at-Rest

Microsoft Azure inclui ferramentas para salvaguardar os dados de acordo com as necessidades de segurança e conformidade da sua empresa. Este documento se concentra em:

- Como os dados são protegidos em inatividade em todo o Microsoft Azure
- Aborda os vários componentes que utilizar a implementação de proteção de dados,
- Revisões de prós e contras das abordagens de proteção de gestão de chaves diferentes. 

Encriptação inativa é um requisito de segurança comuns. No Azure, as organizações podem encriptar dados inativos, sem o risco ou o custo de uma solução de gestão de chaves personalizadas. As organizações têm a opção de permitir que o Azure gerir totalmente a encriptação em repouso. Além disso, as organizações têm várias opções para gerenciar atentamente a encriptação ou chaves de encriptação.

## <a name="what-is-encryption-at-rest"></a>O que é a encriptação em repouso?

Encriptação inativa é a codificação (criptografia) de dados, quando ela é persistida. A encriptação em designs de Rest no Azure usar a criptografia simétrica para criptografar e descriptografar grandes quantidades de dados rapidamente, de acordo com um modelo conceitual simple:

- Uma chave de criptografia simétrica é utilizada para encriptar dados, como ele é escrito para o armazenamento. 
- A mesma chave de encriptação é utilizada para desencriptar esses dados, como ele é readied para utilização na memória.
- Os dados podem ser particionados e chaves diferentes podem ser utilizadas para cada partição.
- As chaves devem ser armazenadas numa localização segura com o controlo de acesso com base na identidade e políticas de auditoria. Chaves de encriptação de dados, muitas vezes, são encriptadas com a criptografia assimétrica para limitar ainda mais o acesso.

Na prática, principais cenários de gerenciamento e controle, bem como as garantias de disponibilidade e dimensionamento, necessitam de construções adicionais. Encriptação do Azure da Microsoft em Rest conceitos e componentes estão descritos abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>A finalidade de encriptação em repouso

Encriptação em repouso fornece proteção de dados para dados armazenados (Inativos). Ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware no qual os dados são armazenados e, em seguida, comprometer os dados contidos. Num ataque, unidade de disco rígido de um servidor pode ter sido mishandled durante a manutenção, permitindo que um atacante remover o disco rígido. Mais tarde o invasor poderia colocar o disco rígido num computador sob seu controle a tentar aceder os dados. 

Encriptação em repouso foi concebida para impedir que o invasor acedam a não encriptada dados ao garantir que os dados são encriptados quando no disco. Se um invasor obtiver uma unidade de disco rígido com os dados encriptados mas não as chaves de encriptação, o atacante tem derrotar a encriptação de ler os dados. Este ataque é muito mais complexo e o consumo de recursos que acedam aos dados não encriptados numa unidade de disco rígido. Por esse motivo, a encriptação em repouso é altamente recomendável e é um requisito de alta prioridade para muitas organizações. 

Também pode ser necessária a encriptação em repouso pela necessidade de uma organização de iniciativas de governação e conformidade de dados. Os regulamentos da indústria e governamentais, como HIPAA, PCI e FedRAMP, criar o layout garantias específicas sobre os requisitos de proteção e encriptação de dados. Encriptação inativa é uma medida obrigatória necessária para a compatibilidade com alguns desses regulamentos.

Para além das que satisfaça os requisitos de regulamentação e conformidade, a encriptação em repouso fornece proteção de defesa em profundidade. Microsoft Azure fornece uma plataforma em conformidade para serviços, aplicações e dados. Ele também fornece o recurso abrangente e segurança física, controlo de acesso de dados e a auditoria. No entanto, é importante fornecer as medidas de segurança de "sobreposição" adicionais no caso de uma das outras medidas de segurança falhar e a encriptação em repouso fornece uma medida de segurança

Microsoft está empenhada em criptografia de opções de rest em serviços de nuvem e dando aos clientes controlo das chaves de encriptação e os registos de utilização de chaves. Além disso, a Microsoft está trabalhando para encriptar todos os dados de cliente Inativos por predefinição.

## <a name="azure-encryption-at-rest-components"></a>Encriptação do Azure nos componentes de Rest

Conforme descrito anteriormente, o objetivo de encriptação em repouso é que os dados que são persistidos no disco são encriptados com uma chave secreta de criptografia. Para alcançar a criação de chaves segura esse objetivo, armazenamento, o controlo de acesso e gestão das chaves de encriptação devem ser fornecidos. Embora os detalhes podem variar, serviços do Azure a encriptação em implementações da Rest podem ser descritos em termos ilustrados no diagrama seguinte.

![Componentes](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A localização de armazenamento de chaves de encriptação e controlo de acesso a essas chaves é central para uma encriptação no modelo rest. As chaves tem de ser altamente protegidos, mas gerenciável por utilizadores especificados e disponíveis para serviços específicos. Para os serviços do Azure, Azure Key Vault é a solução de armazenamento de chaves recomendada e fornece uma experiência de gerenciamento comuns em todos os serviços. As chaves são armazenadas e gerenciadas em cofres de chaves, e acesso a um cofre de chaves pode ser fornecido para utilizadores ou serviços. O Azure Key Vault suporta a criação de cliente de chaves ou importação de chaves de cliente para utilização em cenários de chave de encriptação gerida pelo cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

Permissões para utilizar as chaves armazenadas no Azure Key Vault, para gerir ou acessá-los para a encriptação na encriptação de inativos e a descriptografia, podem ser atribuídas às contas do Azure Active Directory. 

### <a name="key-hierarchy"></a>Hierarquia de chaves

Mais do que uma chave de encriptação é usada numa encriptação na implementação de rest. Criptografia assimétrica é útil para estabelecer a confiança e de autenticação necessário para a gestão e acesso de chave. A criptografia simétrica é mais eficiente para encriptação em massa e de desencriptação, permitindo a encriptação mais forte e um melhor desempenho. Limitar a utilização de uma chave de encriptação único diminui o risco de que a chave será comprometida e o custo da nova criptografia quando uma chave têm de ser substituída. Azure encriptações em modelos de rest utilizam uma hierarquia de chaves constituída pelos seguintes tipos de chaves:

- **Chave de encriptação de dados (DEK)** – uma chave simétrica de AES256 utilizada para encriptar uma partição ou o bloco de dados.  Um único recurso pode ter partições muitos e muitos chaves de encriptação de dados. Criptografar cada bloco de dados com uma chave diferente torna a ataques de análise de criptografia mais difícil. Acesso a DEKs é necessária para a instância de fornecedor ou a aplicação de recurso que está a encriptar e desencriptar um bloco específico. Quando um DEK é substituída por uma nova chave apenas os dados no seu bloco associado tem de ser encriptados novamente com a nova chave.
- **Chave de encriptação de chaves (KEK)** – uma chave de criptografia assimétrica utilizada para encriptar as chaves de encriptação de dados. Utilização de uma chave de encriptação de chave permite que as chaves de encriptação de dados propriamente ditos sejam encriptados e controlado. A entidade que tem acesso para a KEK pode ser diferente do que a entidade que requer o DEK. Uma entidade pode mediador acesso a DEK para limitar o acesso de cada DEK para uma partição específica. Uma vez que a KEK é necessário para desencriptar os DEKs, a KEK é, efetivamente, um ponto único pelo qual DEKs podem ser efetivamente eliminados pela eliminação da KEK.

As chaves de encriptação de dados criptografados com chaves de encriptação de chave são armazenadas em separado e apenas uma entidade com acesso à chave de encriptação de chave pode obter as chaves de encriptação dados criptografados com essa chave. Diferentes modelos de armazenamento de chaves são suportados. Vamos discutir cada modelo mais detalhadamente mais tarde na secção seguinte.

## <a name="data-encryption-models"></a>Modelos de encriptação de dados

Uma compreensão dos vários modelos de encriptação e seus prós e contras é essencial para entender como os vários fornecedores de recursos no Azure implementam a encriptação em repouso. Estas definições são partilhadas entre todos os fornecedores de recursos no Azure para garantir que o idioma e taxonomia comuns. 

Existem três cenários para a encriptação do lado do servidor:

- Utilizar chaves geridas pelo serviço de encriptação do lado do servidor
    - Fornecedores de recursos do Azure realizar as operações de encriptação e desencriptação
    - Microsoft gerencia as chaves
    - Funcionalidade de cloud completa

- Encriptação do lado do servidor a utilizar chaves geridas pelo cliente no Azure Key Vault
    - Fornecedores de recursos do Azure realizar as operações de encriptação e desencriptação
    - Cliente controla as chaves através do Azure Key Vault
    - Funcionalidade de cloud completa

- Encriptação do lado do servidor a utilizar chaves geridas pelo cliente em hardware controlado pelo cliente
    - Fornecedores de recursos do Azure realizar as operações de encriptação e desencriptação
    - Cliente controla as chaves no hardware controlado pelo cliente
    - Funcionalidade de cloud completa

Para a encriptação do lado do cliente, considere o seguinte:

- Serviços do Azure não podem ver os dados descriptografados
- Os clientes gerir e armazenam as chaves no local (ou em outras proteger arquivos). As chaves não estão disponíveis para os serviços do Azure
- Funcionalidades da cloud reduzida

Os modelos de encriptação suportados no Azure dividido em dois grupos principais: "Criptografia de cliente" e "encriptação do lado do servidor" mencionado anteriormente. Independente da encriptação no modelo rest utilizado, sempre os serviços do Azure, recomendamos a utilização de um transporte seguro, como TLS ou HTTPS. Por conseguinte, a encriptação no transporte deve ser resolvida pelo protocolo de transporte e não deve ser um fator principal na determinação de qual encriptação no modelo de rest para utilizar.

### <a name="client-encryption-model"></a>Modelo de encriptação do cliente

Modelo de encriptação do cliente refere-se a encriptação que é executada fora do fornecedor de recursos ou do Azure, o serviço ou aplicação de chamada. A criptografia pode ser executada, a aplicação de serviço no Azure ou uma aplicação em execução no Centro de dados de cliente. Em ambos os casos, ao tirar partido deste modelo de encriptação, o fornecedor de recursos do Azure, recebe um blob encriptado dos dados sem a capacidade para desencriptar os dados de qualquer forma ou de ter acesso às chaves de encriptação. Nesse modelo, a gestão de chaves é feita pela aplicação/serviço de chamada e opacos para o serviço do Azure.

![Cliente](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de encriptação do lado do servidor

Modelos de encriptação do lado do servidor referem-se a encriptação é efetuada pelo serviço do Azure. Esse modelo, o fornecedor de recursos efetua as operações de encriptar e desencriptar. Por exemplo, o armazenamento do Azure podem receber dados em operações de texto sem formatação e irá efetuar a encriptação e desencriptação internamente. O fornecedor de recursos poderá utilizar as chaves de encriptação que são geridas pela Microsoft ou pelo cliente consoante a configuração fornecida. 

![Servidor](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de gestão de chaves de encriptação do lado do servidor

Cada um a encriptação do lado do servidor em modelos de rest implica características distintas da gestão de chaves. Isto inclui onde e como as chaves de encriptação são criadas e armazenadas, bem como os modelos de acesso e os procedimentos de rotação de chaves. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Utilizar chaves geridas pelo serviço de encriptação do lado do servidor

Para muitos clientes, o requisito essencial é garantir que os dados são encriptados sempre que estejam inativos. Encriptação do lado do servidor usando esse modelo de permite de chaves gerida pelo serviço, permitindo que os clientes marcar o recurso específico (conta de armazenamento, BD SQL, etc.) para a encriptação e deixando todos os aspetos de gestão de chaves, como emissão de chave, rotação e cópia de segurança para a Microsoft . A maioria dos serviços do Azure que suportam encriptação em repouso normalmente suporta este modelo de descarga o gerenciamento de chaves de encriptação para o Azure. O fornecedor de recursos do Azure cria as chaves, coloca-os no armazenamento seguro e obtém-los quando necessário. Isso significa que o serviço tem acesso total para as chaves e o serviço tem controle total sobre o gerenciamento de ciclo de vida de credencial.

![Gerido](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Encriptação do lado do servidor utilizar chaves geridas pelo serviço, por conseguinte, rapidamente aborda a necessidade de ter a encriptação em repouso com baixa sobrecarga para o cliente. Se estiver disponível um cliente normalmente é aberto o portal do Azure para a subscrição de destino e o fornecedor de recursos e verifica uma caixa que indica, gostaria de ter os dados sejam encriptados. Em alguns gerenciadores de recursos a encriptação do lado do servidor com chaves geridas pelo serviço está ativada por predefinição.

Encriptação do lado do servidor com chaves geridas pelo Microsoft implica que o serviço tem acesso total ao armazenar e gerir as chaves. Embora alguns clientes talvez queira gerenciar as chaves porque acham que obtêm maior segurança, o custo e o risco associados a uma solução de armazenamento de chaves devem ser considerados ao avaliar esse modelo. Em muitos casos, uma organização pode determinar que os riscos de uma solução no local ou de restrições de recursos podem ser maiores do que o risco de gestão na cloud de criptografia em chaves de rest.  No entanto, esse modelo pode não ser suficiente para as organizações que têm requisitos para controlar a criação ou o ciclo de vida das chaves de criptografia ou ter pessoal diferente de gerir chaves de encriptação de um serviço que aquelas gestão do serviço (ou seja, segregação de gerenciamento de chaves do modelo de gestão geral para o serviço).

##### <a name="key-access"></a>Acesso a chaves

Quando é utilizada a encriptação do lado do servidor com chaves geridas pelo serviço, a criação da chave, armazenamento e acesso de serviço são todos os geridos pelo serviço. Normalmente, os fornecedores de recursos do Azure básico irão armazenar as chaves de encriptação de dados num arquivo que está próximo dos dados e rapidamente disponível e acessível ao mesmo tempo as chaves de encriptação de chave são armazenados num arquivo interno seguro.

**Vantagens**

- Configuração simples
- Microsoft gerir a rotação de chaves, a cópia de segurança e a redundância
- Cliente não tem o custo associado a implementação ou o risco de um esquema de gestão de chaves personalizadas.

**Desvantagens**

- Nenhum controle de cliente sobre as chaves de encriptação (especificação da chave, ciclo de vida, revogação, etc.)
- Nenhuma capacidade para separar a partir do modelo de gestão geral para o serviço de gestão de chaves

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação do lado do servidor a utilizar chaves geridas pelo cliente no Azure Key Vault 

Para cenários em que o requisito encriptar os dados em repouso e controlar a encriptação de clientes de chaves podem utilizar com chaves geridas pelo cliente no Cofre de chaves de encriptação do lado do servidor. Alguns serviços podem armazenar apenas a chave de encriptação de chave de raiz no Azure Key Vault e armazene a chave de encriptação de dados encriptado numa localização interna mais próximo aos dados. Em que os clientes de cenário podem trazer suas próprias chaves para o Cofre de chave (BYOK – traga a sua própria chave), ou gerar novos e usá-los para encriptar os recursos desejados. Enquanto o fornecedor de recursos efetua as operações de criptografia e descriptografia utiliza a chave configurada como a chave de raiz para todas as operações de encriptação. 

##### <a name="key-access"></a>Acesso a chaves

O modelo de encriptação do lado do servidor com chaves geridas pelo cliente no Azure Key Vault envolve o serviço de aceder às chaves de para encriptar e desencriptar conforme necessário. Encriptação em chaves de rest são feitas acessível a um serviço através de uma política de controlo de acesso. Esta política concede o acesso de identidade de serviço para receber a chave. Um serviço do Azure em execução em nome de uma subscrição associada pode ser configurado com uma identidade nessa subscrição. O serviço pode efetuar a autenticação do Azure Active Directory e receber um token de autenticação identificar em si como esse serviço agir em nome da subscrição. Esse token pode ser apresentado para o Key Vault para obter uma chave tem sido dado acesso.

Para operações com chaves de encriptação, uma identidade de serviço pode ser concedida acesso a qualquer uma das seguintes operações: desencriptar, encriptar, unwrapKey, wrapKey, verificar, iniciar sessão, obter, listar, atualizar, criar, importar, eliminar, de cópia de segurança e restaurar.

Para obter uma chave para utilização em encriptar ou desencriptar dados em repouso a identidade de serviço que a instância de serviço do Gestor de recursos será executado como tem de ter UnwrapKey (para obter a chave de desencriptação) e WrapKey (para inserir uma chave no Cofre de chaves ao criar uma nova chave).


>[!NOTE] 
>Para obter mais detalhes sobre o Key Vault autorização ver a segura página do Cofre de chaves no [documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Vantagens**

- Controlo total sobre as chaves utilizadas – encriptação de chaves são geridas no Cofre de chaves do cliente sob o controle do cliente.
- Capacidade de criptografar vários serviços para uma principal
- Pode segregar a partir do modelo de gestão geral para o serviço de gestão de chaves
- Pode definir o serviço e a localização da chave em várias regiões

**Desvantagens**

- O cliente tem total responsabilidade para a gestão de acesso a chaves
- O cliente tem total responsabilidade para a gestão de ciclo de vida de chave
- Sobrecarga adicional de instalação e configuração

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Encriptação do lado do servidor com chaves geridas pelo serviço no hardware controlado pelo cliente

Alguns serviços do Azure permitem o modelo de gestão de chaves de anfitrião Your Own Key (HYOK). Esse modo de gestão é útil em cenários onde há a necessidade de encriptar dados inativos e gerir as chaves num repositório proprietária fora do controlo da Microsoft. Nesse modelo, o serviço tem de obter a chave de um site externo. Garantias de desempenho e disponibilidade são afetadas e configuração é mais complexa. Além disso, uma vez que o serviço tem acesso para o DEK durante as operações de criptografia e descriptografia as garantias de segurança geral desse modelo são semelhantes às quando as chaves são geridas pelo cliente no Azure Key Vault.  Como resultado, esse modelo não é adequado para a maioria das organizações, exceto se tiverem requisitos de gestão de chaves específico. Devido a essas limitações, a maioria dos serviços do Azure não suportam a encriptação do lado do servidor com chaves geridas pelo servidor no hardware controlado pelo cliente.

##### <a name="key-access"></a>Acesso a chaves

Quando é utilizada com chaves geridas pelo serviço no hardware controlado pelo cliente de encriptação do lado do servidor as chaves são mantidas num sistema configurado pelo cliente. Serviços do Azure que suportam este modelo fornecem um meio de estabelecer uma ligação segura para um cliente fornecido pelo arquivo de chaves.

**Vantagens**

- Controlo total sobre a chave de raiz utilizado – encriptação de chaves são geridas por um arquivo de cliente fornecido
- Capacidade de criptografar vários serviços para uma principal
- Pode segregar a partir do modelo de gestão geral para o serviço de gestão de chaves
- Pode definir o serviço e a localização da chave em várias regiões

**Desvantagens**

- Total responsabilidade para o armazenamento de chaves, segurança, desempenho e disponibilidade
- Total responsabilidade para a gestão de acesso a chaves
- Total responsabilidade para a gestão de ciclo de vida de chave
- Configuração significativa, configuração e os custos de manutenção em curso
- Dependência aumentada na disponibilidade de rede entre o Centro de dados do cliente e os datacenters do Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Encriptação em inatividade nos serviços cloud da Microsoft

Serviços Cloud da Microsoft são utilizados em todos os modelos de nuvem três: IaaS, PaaS, SaaS. Abaixo, pode encontrar exemplos de como eles se relacionam em cada modelo:

- Serviços de software, chamado de Software como um servidor ou o SaaS, com a aplicação fornecida pela cloud como o Office 365.
- Serviços de plataforma que clientes tirar partido da cloud nas respetivas aplicações, utilizando a cloud para armazenamento, análise e funcionalidade de barramento de serviço.
- Serviços de infraestrutura ou de infraestrutura como serviço (IaaS) no qual cliente implementa sistemas operativos e aplicações alojadas na cloud e, possivelmente, tirar partido de outros serviços em nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Encriptação em repouso para clientes de SaaS

Software como um clientes de serviço (SaaS), normalmente, tem a encriptação em repouso disponível em cada serviço ou ativado. Office 365 tem várias opções para os clientes verificar ou ativar a encriptação em repouso. Para obter informações sobre serviços do Office 365, consulte [Encryption no Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Encriptação em repouso para clientes de PaaS

Plataforma como dados de um cliente de serviço (PaaS) normalmente reside num ambiente de execução de aplicativos e quaisquer fornecedores de recursos do Azure utilizada para armazenar dados do cliente. Para ver a encriptação em Opções de rest disponíveis, examine a tabela abaixo para as plataformas de armazenamento e a aplicação que utiliza. Onde for suportado, são fornecidas hiperligações para instruções sobre como ativar a encriptação em repouso para cada fornecedor de recursos. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Encriptação em repouso para clientes de IaaS

Infraestrutura como um clientes de serviço (IaaS) pode ter uma variedade de serviços e aplicações em utilização. Serviços de IaaS podem ativar a encriptação em repouso no seu Azure alojado máquinas virtuais e os VHDs com o Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Armazenamento encriptado

Como PaaS, soluções IaaS podem aproveitar a outros serviços do Azure que armazenam dados Inativos encriptados. Nestes casos, pode ativar a encriptação no suporte da Rest conforme fornecido por cada serviço do Azure consumido. A tabela abaixo enumera o armazenamento de principais, serviços e plataformas de aplicações e o modelo de encriptação em repouso suportado. Onde for suportado, são fornecidas hiperligações para instruções sobre como ativar a encriptação em repouso. 

#### <a name="encrypted-compute"></a>Computação encriptada

Uma encriptação completa na solução de Rest requer que os dados nunca são persistidos no formato descriptografado. Enquanto estiver em uso, num servidor de carregar os dados na memória, os dados podem ser mantidos localmente de várias formas, incluindo o ficheiro de paginação do Windows, um despejo de falha e o registo que a aplicação pode efetuar. Para garantir que estes dados são encriptados em descanso, aplicativos de IaaS podem utilizar o Azure Disk Encryption numa máquina de virtual IaaS do Azure (Windows ou Linux) e o disco virtual. 

#### <a name="custom-encryption-at-rest"></a>Personalizado encriptação inativa

Recomenda-se que, sempre que possível, aplicativos de IaaS aproveitam os Azure Disk Encryption e encriptação fornecida pelo serviços do Azure consumidos de opções de Rest. Em alguns casos, tais como requisitos de encriptação irregulares ou armazenamento baseado em do não pertencente ao Azure, um desenvolvedor de um aplicativo de IaaS, poderá ter de implementar a criptografia em rest propriamente ditos. Os desenvolvedores de IaaS soluções podem melhor integração com expectativas de gestão e o cliente do Azure ao tirar partido de determinados componentes do Azure. Especificamente, os desenvolvedores devem usar o serviço Azure Key Vault para fornecer armazenamento de chaves seguro, bem como para fornecer aos clientes opções de gestão de chaves consistente com dos serviços de plataforma mais do Azure. Além disso, as soluções personalizadas devem utilizar Azure-Managed identidades de serviço para permitir que contas de serviço para aceder a chaves de encriptação. Para informações para programadores sobre o Azure Key Vault e identidades de serviço geridas, consulte os respetivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte de modelo de encriptação de fornecedores de recursos do Azure

Serviços do Microsoft Azure cada suporta um ou mais da encriptação em modelos de rest. Para alguns serviços, no entanto, um ou mais dos modelos de encriptação podem não ser aplicáveis. Para os serviços que suportam cenários-chave gerida pelo cliente, eles podem suportar apenas um subconjunto de tipos de chaves do Azure Key Vault suporta chaves de encriptação de chave. Além disso, os serviços podem versão suporte para estes cenários e tipos de chaves em diferentes agendamentos. Esta secção descreve a encriptação no suporte de rest no momento da redação deste artigo para cada um dos serviços de armazenamento de dados do Azure principais.

### <a name="azure-disk-encryption"></a>Encriptação de disco do Azure

Todos os clientes utilizando a infraestrutura do Azure como um serviço (IaaS) funcionalidades podem alcançar encriptação em repouso para as suas VMs de IaaS e os discos através de encriptação de disco do Azure. Para obter mais informações sobre a encriptação de disco do Azure, consulte a [documentação do Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Storage do Azure

Todos os serviços de armazenamento do Azure (armazenamento de BLOBs, armazenamento de filas, o armazenamento de tabelas e ficheiros do Azure) suportam a encriptação do lado do servidor em repouso, com alguns serviços de suporte de chaves geridas pelo cliente e a encriptação do lado do cliente.  

- Lado do servidor: Todos os serviços de armazenamento do Azure ativar a encriptação do lado do servidor por predefinição, com chaves geridas pelo serviço, que é transparente para a aplicação. Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](https://docs.microsoft.com/azure/storage/storage-service-encryption). Armazenamento de Blobs do Azure e de ficheiros do Azure também suportam chaves geridas pelo cliente de RSA 2048 bits no Azure Key Vault. Para obter mais informações, consulte [encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Lado do cliente: Blobs, tabelas e filas do Azure suportam a encriptação do lado do cliente. Quando utilizar a encriptação do lado do cliente, os clientes encriptam os dados e carregar os dados como um blob encriptado. Gestão de chaves é feita pelo cliente. Para obter mais informações, consulte [encriptação do lado do cliente e o Azure Key Vault para o armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Atualmente, a base de dados SQL do Azure suporta encriptação em repouso para cenários de encriptação do lado do cliente e de lado de serviço gerida pela Microsoft.

Suporte para encriptação de servidor atualmente é fornecido por meio do recurso chamado criptografia transparente de dados SQL. Assim que um cliente de base de dados do Azure SQL permite que o chave TDE são automaticamente criados e geridos para os mesmos. Pode ser ativada a encriptação em repouso nos níveis de base de dados e o servidor. A partir de Junho de 2017 [encriptação de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) está ativada por predefinição nas bases de dados recentemente criados. Base de dados SQL do Azure suporta chaves geridas pelo cliente de RSA 2048 bits no Azure Key Vault. Para obter mais informações, consulte [encriptação de dados transparente com suporte de traga a sua própria chave para a base de dados do Azure SQL e o armazém de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Encriptação do lado do cliente de dados SQL Database do Azure é suportada através da [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funcionalidade. Sempre encriptado usa uma chave que criado e armazenado pelo cliente. Os clientes podem armazenar a chave mestra num arquivo de certificados do Windows, o Azure Key Vault ou um módulo de segurança de Hardware local. Com o SQL Server Management Studio, os utilizadores do SQL escolha que eles gostariam de utilizar para encriptar a coluna de chave.

|                                  |                    | **Modelo de encriptação e gestão de chaves** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Lado do servidor com chave gerida pelo serviço**     | **Lado do servidor usando gerida pelo cliente no Cofre de chaves**             | **Lado do cliente com o cliente gerido**      |
| **Armazenamento e bases de dados**        |                    |                    |                    |
| Disk (IaaS)                      | -                  | Sim, RSA de 2048 bits  | -                  |
| SQL Server (IaaS)                | Sim                | Sim, RSA de 2048 bits  | Sim                |
| SQL do Azure (base de dados/Data Warehouse) | Sim                | Sim, RSA de 2048 bits  | Sim                |
| SQL do Azure (instância gerida da base de dados) | Sim                | Pré-visualização, RSA de 2048 bits  | Sim                |
| Armazenamento do Azure (Blobs de bloco/páginas) | Sim                | Sim, RSA de 2048 bits  | Sim                |
| Armazenamento do Azure (ficheiros)            | Sim                | Sim, RSA de 2048 bits  | -                  |
| Armazenamento do Azure (tabelas, filas)   | Sim                | -                  | Sim                |
| Cosmos DB (Document DB)          | Sim                | -                  | -                  |
| StorSimple                       | Sim                | -                  | Sim                |
| Cópia de segurança                           | Sim                | -                  | Sim                |
| **Inteligência e análise**   |                    |                    |                    |
| Azure Data Factory               | Sim                | -                  | -                  |
| Azure Machine Learning           | -                  | Pré-visualização, RSA de 2048 bits | -                  |
| Azure Stream Analytics           | Sim                | -                  | -                  |
| HDInsight (armazenamento de Blobs do Azure)   | Sim                | -                  | -                  |
| HDInsight (armazenamento do Data Lake)    | Sim                | -                  | -                  |
| Apache Kafka para HDInsight       | Sim                | Pré-visualização, todos os comprimentos RSA | -                  |
| Azure Data Lake Store            | Sim                | Sim, RSA de 2048 bits  | -                  |
| Catálogo de Dados do Azure               | Sim                | -                  | -                  |
| Power BI                         | Sim                | -                  | -                  |
| **Serviços de IoT**                 |                    |                    |                    |
| IoT Hub                          | -                  | -                  | Sim                |
| Service Bus                      | Sim                | -                  | Sim                |
| Hubs de Eventos                       | Sim                | -                  | -                  |
| Event Grid                       | Sim                | -                  | -                  |


## <a name="conclusion"></a>Conclusão

Proteção de dados de clientes armazenados nos serviços do Azure é de fundamental importância para Microsoft. Todos os Azure hosted services são seu compromisso de fornecer encriptação nas opções de Rest. Serviços fundamentais, como o armazenamento do Azure, a base de dados SQL do Azure e a análise de chave e os serviços de inteligência já fornecem criptografia de opções de Rest. Alguns destes serviços suportam chaves dos clientes controlado e encriptação do lado do cliente, bem como as chaves geridas pelo serviço e encriptação. Serviços do Microsoft Azure amplamente a melhorar encriptação no momento da disponibilidade do Rest e novas opções estão planeadas para disponibilidade geral e pré-visualização nos próximos meses.
