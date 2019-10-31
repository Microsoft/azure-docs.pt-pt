---
title: Isolamento na nuvem pública do Azure | Microsoft Docs
description: Saiba como o Azure fornece isolamento contra usuários mal-intencionados e não-mal-intencionados e oferece várias opções de isolamento para arquitetos.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: 5e6910db7765c4cb8f151401a6803e6d4d3f998e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159751"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolamento na nuvem pública do Azure
O Azure permite que você execute aplicativos e VMs (máquinas virtuais) na infraestrutura física compartilhada. Uma das principais motivações econômicas para a execução de aplicativos em um ambiente de nuvem é a capacidade de distribuir o custo de recursos compartilhados entre vários clientes. Essa prática de multilocação melhora a eficiência por meio da multiplexação de recursos entre diferentes clientes a baixo custo. Infelizmente, ele também apresenta o risco de compartilhar servidores físicos e outros recursos de infraestrutura para executar seus aplicativos confidenciais e VMs que podem pertencer a um usuário arbitrário e potencialmente mal-intencionado.

Este artigo descreve como o Azure fornece isolamento contra usuários mal-intencionados e não mal-intencionados e serve como um guia para arquitetar soluções de nuvem oferecendo várias opções de isolamento para arquitetos.

## <a name="tenant-level-isolation"></a>Isolamento no nível do locatário
Um dos principais benefícios da computação em nuvem é o conceito de uma infraestrutura compartilhada e comum entre vários clientes simultaneamente, levando a economias de escala. Esse conceito é chamado de multilocação. A Microsoft trabalha continuamente para garantir que a arquitetura multilocatário do Microsoft Cloud o Azure dê suporte à segurança, confidencialidade, privacidade, integridade e padrões de disponibilidade.

Na área de trabalho ativada na nuvem, um inquilino pode ser definido com um cliente ou uma organização que possui e gere uma instância específica do referido serviço em nuvem. Com a plataforma de identidade fornecida pelo Microsoft Azure, um locatário é simplesmente uma instância dedicada do Azure Active Directory (AD do Azure) que sua organização recebe e possui quando se inscreve em um serviço de nuvem da Microsoft.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Tal como um edifício de escritórios empresariais é um recurso seguro específico apenas para a sua organização, um diretório do Azure AD também foi concebido para ser um recurso com utilização segura apenas para a sua organização. A arquitetura do Azure AD impede que os dados do cliente e as informações de identidade se misturem. Isto significa que os utilizadores e administradores de um diretório do Azure AD não podem, acidental ou intencionalmente, aceder aos dados de outro diretório.

### <a name="azure-tenancy"></a>Aluguel do Azure
A locação do Azure (assinatura do Azure) refere-se a uma relação de "cliente/cobrança" e um [locatário](../../active-directory/develop/quickstart-create-new-tenant.md) exclusivo em [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). O isolamento no nível do locatário no Microsoft Azure é obtido usando Azure Active Directory e [controles baseados em função](../../role-based-access-control/overview.md) oferecidos por ele. Cada assinatura do Azure é associada a um diretório Azure Active Directory (AD).

Usuários, grupos e aplicativos desse diretório podem gerenciar recursos na assinatura do Azure. Você pode atribuir esses direitos de acesso usando o portal do Azure, as ferramentas de linha de comando do Azure e as APIs de gerenciamento do Azure. Um locatário do Azure AD é logicamente isolado usando limites de segurança para que nenhum cliente possa acessar ou comprometer os colocatários, de forma mal-intencionada ou acidental. O Azure AD é executado em servidores "bare-metal" isolados em um segmento de rede segregado, onde a filtragem de pacotes no nível de host e o Firewall do Windows bloqueiam conexões e tráfego indesejados.

- O acesso aos dados no Azure AD requer autenticação de usuário por meio de um serviço de token de segurança (STS). As informações sobre a existência do usuário, o estado habilitado e a função são usadas pelo sistema de autorização para determinar se o acesso solicitado ao locatário de destino está autorizado para este usuário nesta sessão.

![Aluguel do Azure](./media/isolation-choices/azure-isolation-fig1.png)


- Os locatários são contêineres discretos e não há nenhuma relação entre eles.

- Nenhum acesso entre locatários, a menos que o administrador de locatários o conceda por meio da Federação ou do provisionamento de contas de usuário de outros locatários.

- O acesso físico aos servidores que compõem o serviço do Azure AD e acesso direto aos sistemas de back-end do Azure AD é restrito.

- Os usuários do Azure AD não têm acesso a ativos físicos ou locais e, portanto, não é possível ignorar as verificações de política de RBAC lógicas declaradas a seguir.

Para necessidades de diagnóstico e manutenção, um modelo operacional que emprega um sistema de elevação de privilégio just-in-time é necessário e usado. Azure AD Privileged Identity Management (PIM) apresenta o conceito de administrador qualificado. [Administradores qualificados](../../active-directory/privileged-identity-management/pim-configure.md) devem ser usuários que precisam de acesso privilegiado agora e, em seguida, não todos os dias. A função está inativa até que o utilizador precise de acesso. Nessa altura, o utilizador realiza um processo de ativação e torna-se num administrador ativo durante uma quantidade pré-determinada de tempo.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory hospeda cada locatário em seu próprio contêiner protegido, com políticas e permissões para e dentro do contêiner exclusivamente de propriedade e gerenciado pelo locatário.

O conceito de contêineres de locatário está profundamente refinado no serviço de diretório em todas as camadas, desde portais até o armazenamento persistente.

Mesmo quando os metadados de vários locatários Azure Active Directory são armazenados no mesmo disco físico, não há nenhuma relação entre os contêineres além do que é definido pelo serviço de diretório, que, por sua vez, é determinado pelo administrador de locatários.

### <a name="azure-role-based-access-control-rbac"></a>RBAC (controle de acesso baseado em função) do Azure
O [RBAC (controle de acesso baseado em função) do Azure](../../role-based-access-control/overview.md) ajuda você a compartilhar vários componentes disponíveis em uma assinatura do Azure fornecendo gerenciamento de acesso refinado para o Azure. O RBAC do Azure permite separar as tarefas dentro de sua organização e conceder acesso com base no que os usuários precisam para executar seus trabalhos. Em vez de fornecer a todos permissões irrestritas na assinatura ou nos recursos do Azure, você pode permitir apenas determinadas ações.

O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recursos:

- O **proprietário** tem acesso completo a todos os recursos, incluindo o direito de delegar acesso a outras pessoas.

- O **colaborador** pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.

- O **leitor** pode exibir os recursos existentes do Azure.

![Controle de acesso baseado em função do Azure](./media/isolation-choices/azure-isolation-fig3.png)

O restante das funções de RBAC no Azure permite o gerenciamento de recursos específicos do Azure. Por exemplo, a função colaborador da máquina virtual permite que o usuário crie e gerencie máquinas virtuais. Ele não dá acesso à rede virtual do Azure ou à sub-rede à qual a máquina virtual se conecta.

[Funções internas de RBAC](../../role-based-access-control/built-in-roles.md) listam as funções disponíveis no Azure. Ele especifica as operações e o escopo que cada função interna concede aos usuários. Se você estiver procurando definir suas próprias funções para obter ainda mais controle, consulte como criar [funções personalizadas no RBAC do Azure](../../role-based-access-control/custom-roles.md).

Alguns outros recursos para Azure Active Directory incluem:
- O Azure AD permite o SSO para aplicativos SaaS, independentemente de onde eles estão hospedados. Algumas aplicações estão federadas com o Azure AD e outras utilizam SSO com palavra-passe. Os aplicativos federados também podem dar suporte ao provisionamento de usuário e à [compartimentação de senha](https://www.techopedia.com/definition/31415/password-vault).

- O acesso aos dados do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado através da autenticação. Cada conta de armazenamento tem uma chave primária ([chave de conta de armazenamento](../../storage/common/storage-create-storage-account.md)ou Sak) e uma chave de segredo secundária (a assinatura de acesso compartilhado ou SAS).

- O Azure AD fornece identidade como um serviço por meio de Federação usando [serviços de Federação do Active Directory (AD FS)](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md), sincronização e replicação com diretórios locais.

- A [autenticação multifator do Azure](../../active-directory/authentication/multi-factor-authentication.md) é o serviço de autenticação multifator que exige que os usuários verifiquem as entradas usando um aplicativo móvel, uma chamada telefônica ou uma mensagem de texto. Ele pode ser usado com o Azure AD para ajudar a proteger recursos locais com o servidor de autenticação multifator do Azure e também com aplicativos e diretórios personalizados usando o SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite que você ingresse máquinas virtuais do Azure em um domínio Active Directory sem implantar controladores de domínio. Você pode entrar nessas máquinas virtuais com suas credenciais Active Directory corporativas e administrar máquinas virtuais ingressadas no domínio usando Política de Grupo para impor linhas de base de segurança em todas as máquinas virtuais do Azure.

- O [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) fornece um serviço de gerenciamento de identidade global altamente disponível para aplicativos voltados para o consumidor que são dimensionados para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Seus consumidores podem entrar em todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criando credenciais.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolamento dos administradores da Microsoft & exclusão de dados
A Microsoft toma medidas fortes para proteger seus dados contra acesso inadequado ou uso por pessoas não autorizadas. Esses controles e processos operacionais são apoiados pelos [termos de serviços online](https://aka.ms/Online-Services-Terms), que oferecem compromissos contratuais que regem o acesso aos seus dados.

-   Os engenheiros da Microsoft não têm acesso padrão aos seus dados na nuvem. Em vez disso, eles recebem acesso, sob supervisão de gerenciamento, somente quando necessário. Esse acesso é cuidadosamente controlado e registrado em log e revogado quando não é mais necessário.

-   A Microsoft pode contratar outras empresas para fornecer serviços limitados em seu nome. Os subcontratados podem acessar os dados do cliente somente para fornecer os serviços para os quais nós os contratamos para fornecer, e eles estão proibidos de usá-lo para qualquer outra finalidade. Além disso, eles são associados contratualmente para manter a confidencialidade das informações dos nossos clientes.

Os serviços corporativos com certificações auditadas, como ISO/IEC 27001, são verificados regularmente pela Microsoft e por empresas de auditoria credenciadas, que executam auditorias de exemplo para atestar o acesso, apenas para fins comerciais legítimos. Você sempre pode acessar seus próprios dados do cliente a qualquer momento e por qualquer motivo.

Se você excluir qualquer dado, Microsoft Azure excluirá os dados, incluindo todas as cópias em cache ou de backup. Para serviços dentro do escopo, essa exclusão ocorrerá dentro de 90 dias após o término do período de retenção. (Os serviços no escopo são definidos na seção termos de processamento de dados de nossos [termos de serviços online](https://aka.ms/Online-Services-Terms).)

Se uma unidade de disco usada para armazenamento sofre uma falha de hardware, ela é [apagada ou destruída](https://microsoft.com/trustcenter/privacy/you-own-your-data) com segurança antes que a Microsoft a retorne ao fabricante para substituição ou reparo. Os dados na unidade são substituídos para garantir que os dados não possam ser recuperados por nenhum meio.

## <a name="compute-isolation"></a>Isolamento de computação
O Microsoft Azure fornece vários serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias de computação & serviços que podem ser escalados verticalmente e reduzidos automaticamente para atender às necessidades de seu aplicativo ou empresa. Essas instâncias e serviços de computação oferecem isolamento em vários níveis para proteger dados sem sacrificar a flexibilidade na configuração exigida pelos clientes.

### <a name="isolated-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais isoladas

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V & isolamento raiz do sistema operacional & VMs convidadas da VM raiz
A plataforma de computação do Azure é baseada na virtualização de máquina, o que significa que todo o código do cliente é executado em uma máquina virtual do Hyper-V. Em cada nó do Azure (ou ponto de extremidade de rede), há um hipervisor que é executado diretamente pelo hardware e divide um nó em um número variável de VMs (máquinas virtuais) convidadas.


![Hyper-V & isolamento raiz do sistema operacional & VMs convidadas da VM raiz](./media/isolation-choices/azure-isolation-fig4.jpg)


Cada nó também tem uma VM de raiz especial, que executa o sistema operacional do host. Um limite crítico é o isolamento da VM raiz das VMs convidadas e das VMs convidadas umas das outras, gerenciadas pelo hipervisor e pelo sistema operacional raiz. O emparelhamento do hipervisor/sistema operacional raiz aproveita as décadas de experiência de segurança de sistema operacional da Microsoft e o aprendizado mais recente do Hyper-V da Microsoft, para fornecer isolamento forte de VMs convidadas.

A plataforma Azure utiliza um ambiente virtualizado. As instâncias de usuário operam como máquinas virtuais autônomas que não têm acesso a um servidor de host físico.

O hipervisor do Azure atua como um micro kernel e passa todas as solicitações de acesso de hardware das máquinas virtuais convidadas para o host para processamento usando uma interface de memória compartilhada chamada VMBus. Isto impede os utilizadores de obterem acesso de leitura/escrita/execução não processado ao sistema e atenua o risco de partilha de recursos do sistema.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Algoritmo avançado de posicionamento de VM & proteção contra ataques de canal lateral
Qualquer ataque entre VMs envolve duas etapas: colocar uma VM controlada pelo adversário no mesmo host como uma das VMs vítimas e, em seguida, violar o limite de isolamento para roubar informações de vítima confidenciais ou afetar seu desempenho para ganância ou vandalismo. O Microsoft Azure fornece proteção em ambas as etapas usando um algoritmo avançado de posicionamento de VM e proteção contra todos os ataques de canal do lado conhecidos, incluindo VMs vizinhas com ruídos.

### <a name="the-azure-fabric-controller"></a>O controlador de malha do Azure
O controlador de malha do Azure é responsável por alocar recursos de infraestrutura para cargas de trabalho de locatário e gerencia comunicações unidirecionais do host para máquinas virtuais. O algoritmo de colocação de VM do controlador de malha do Azure é altamente sofisticado e quase impossível de prever como nível de host físico.

![O controlador de malha do Azure](./media/isolation-choices/azure-isolation-fig5.png)

O hipervisor do Azure impõe a memória e a separação de processos entre máquinas virtuais e roteia com segurança o tráfego de rede para locatários do sistema operacional convidado. Isso elimina a possibilidade de ataque de canal lateral no nível de VM.

No Azure, a VM raiz é especial: ela executa um sistema operacional protegido chamado de so raiz que hospeda um FA (agente de malha). O FAs é usado, por sua vez, para gerenciar os agentes convidados (GA) em sistemas operacionais convidados em VMs do cliente. O FAs também gerencia nós de armazenamento.

A coleção de hipervisor do Azure, sistema operacional raiz/FA e VMs do cliente/gás consiste em um nó de computação. O FAs é gerenciado por um controlador de malha (FC), que existe fora dos nós de computação e armazenamento (os clusters de computação e armazenamento são gerenciados por FCs separado). Se um cliente atualizar o arquivo de configuração do aplicativo enquanto ele estiver em execução, o FC se comunicará com o FA, que, em seguida, contata o gás, que notifica a aplicação da alteração de configuração. No caso de uma falha de hardware, o FC encontrará automaticamente o hardware disponível e reiniciará a VM.

![Controlador de malha do Azure](./media/isolation-choices/azure-isolation-fig6.jpg)

A comunicação de um controlador de malha para um agente é unidirecional. O agente implementa um serviço protegido por SSL que responde apenas às solicitações do controlador. Ele não pode iniciar conexões com o controlador ou com outros nós internos privilegiados. O FC trata todas as respostas como se elas não fossem confiáveis.


![Controlador de malha](./media/isolation-choices/azure-isolation-fig7.png)

O isolamento se estende da VM raiz das VMs convidadas e das VMs convidadas umas das outras. Nós de computação também são isolados de nós de armazenamento para maior proteção.


O hipervisor e o sistema operacional do host fornecem filtros de pacote de rede para ajudar a garantir que máquinas virtuais não confiáveis não possam gerar tráfego falsificado ou receber tráfego não endereçado a eles, direcionar o tráfego para pontos de extremidade de infraestrutura protegidos ou enviar/receber tráfego de difusão inadequado.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Regras adicionais configuradas pelo agente do controlador de malha para isolar a VM
Por padrão, todo o tráfego é bloqueado quando uma máquina virtual é criada e, em seguida, o agente do controlador de malha configura o filtro de pacote para adicionar regras e exceções para permitir o tráfego autorizado.

Há duas categorias de regras que são programadas:

-   **Configuração da máquina ou regras de infraestrutura:** Por padrão, toda a comunicação é bloqueada. Há exceções para permitir que uma máquina virtual envie e receba tráfego DHCP e DNS. As máquinas virtuais também podem enviar tráfego para a Internet "pública" e enviar tráfego para outras máquinas virtuais na mesma rede virtual do Azure e no servidor de ativação do sistema operacional. A lista de destinos de saída permitidos das máquinas virtuais não inclui sub-redes de roteador do Azure, gerenciamento do Azure e outras propriedades da Microsoft.

-   **Arquivo de configuração de função:** Isso define as listas de controle de acesso de entrada (ACLs) com base no modelo de serviço do locatário.

### <a name="vlan-isolation"></a>Isolamento de VLAN
Há três VLANs em cada cluster:

![Isolamento de VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)


-   A VLAN principal – interconecta nós de clientes não confiáveis

-   A VLAN FC – contém FCs confiável e sistemas de suporte

-   A VLAN do dispositivo – contém rede confiável e outros dispositivos de infraestrutura

A comunicação é permitida da VLAN FC para a VLAN principal, mas não pode ser iniciada a partir da VLAN principal para a VLAN FC. A comunicação também é bloqueada da VLAN principal para a VLAN do dispositivo. Isso garante que, mesmo que um nó que executa o código do cliente seja comprometido, ele não pode atacar nós em ambas as VLANs de dispositivo ou FC.

## <a name="storage-isolation"></a>Isolamento de armazenamento
### <a name="logical-isolation-between-compute-and-storage"></a>Isolamento lógico entre computação e armazenamento
Como parte de seu design fundamental, Microsoft Azure separa a computação baseada em VM do armazenamento. Essa separação permite que a computação e o armazenamento sejam dimensionados de forma independente, facilitando o fornecimento de multilocação e isolamento.

Portanto, o armazenamento do Azure é executado em hardware separado sem conectividade de rede para a computação do Azure, exceto logicamente. [Isso](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) significa que, quando um disco virtual é criado, o espaço em disco não é alocado para sua capacidade inteira. Em vez disso, é criada uma tabela que mapeia endereços no disco virtual para áreas no disco físico e que a tabela está inicialmente vazia. **Na primeira vez que um cliente grava dados no disco virtual, o espaço no disco físico é alocado e um ponteiro para ele é colocado na tabela.**
### <a name="isolation-using-storage-access-control"></a>Isolamento usando o controle de acesso de armazenamento
O **controle de acesso no armazenamento do Azure** tem um modelo de controle de acesso simples. Cada assinatura do Azure pode criar uma ou mais contas de armazenamento. Cada conta de armazenamento tem uma única chave secreta que é usada para controlar o acesso a todos os dados nessa conta de armazenamento.

![Isolamento usando o controle de acesso de armazenamento](./media/isolation-choices/azure-isolation-fig9.png)

O **acesso aos dados do armazenamento do Azure (incluindo tabelas)** pode ser controlado por meio de um token [SAS (assinatura de acesso compartilhado)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) , que concede acesso ao escopo. A SAS é criada por meio de um modelo de consulta (URL), assinado com o [Sak (chave de conta de armazenamento)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Essa [URL assinada](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) pode ser dada a outro processo (ou seja, delegado), que pode então preencher os detalhes da consulta e fazer a solicitação do serviço de armazenamento. Uma SAS permite que você conceda acesso baseado em tempo aos clientes sem revelar a chave secreta da conta de armazenamento.

A SAS significa que podemos conceder a um cliente permissões limitadas para objetos em nossa conta de armazenamento por um período de tempo especificado e com um conjunto especificado de permissões. Podemos conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta.

### <a name="ip-level-storage-isolation"></a>Isolamento de armazenamento de nível de IP
Você pode estabelecer firewalls e definir um intervalo de endereços IP para seus clientes confiáveis. Com um intervalo de endereços IP, somente os clientes que têm um endereço IP dentro do intervalo definido podem se conectar ao [armazenamento do Azure](../../storage/common/storage-security-guide.md).

Os dados de armazenamento IP podem ser protegidos contra usuários não autorizados por meio de um mecanismo de rede que é usado para alocar um túnel dedicado ou dedicado de tráfego para o armazenamento IP.

### <a name="encryption"></a>Encriptação
O Azure oferece os seguintes tipos de criptografia para proteger os dados:
-   Criptografia em trânsito

-   Encriptação inativa

#### <a name="encryption-in-transit"></a>Criptografia em trânsito
A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o armazenamento do Azure, você pode proteger dados usando:

-   [Criptografia no nível de transporte](../../storage/common/storage-security-guide.md), como https, quando você transfere dados para dentro ou para fora do armazenamento do Azure.

-   [Criptografia de transmissão](../../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como a criptografia SMB 3,0 para compartilhamentos de arquivos do Azure.

-   [Criptografia do lado do cliente](../../storage/common/storage-security-guide.md), para criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografar os dados depois que eles forem transferidos para fora do armazenamento.

#### <a name="encryption-at-rest"></a>Criptografia em repouso
Para muitas organizações, [a criptografia de dados em repouso](isolation-choices.md) é uma etapa obrigatória para a privacidade de dados, a conformidade e a soberania de dados. Há três recursos do Azure que fornecem criptografia de dados que estão "em repouso":

-   [Criptografia do serviço de armazenamento](../../storage/common/storage-security-guide.md) permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no armazenamento do Azure.

-   A [criptografia do lado do cliente](../../storage/common/storage-security-guide.md) também fornece o recurso de criptografia em repouso.

-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) para VMs (máquinas virtuais) ajuda a resolver os requisitos de segurança e conformidade organizacionais criptografando seus discos de VM (incluindo discos de dados e de inicialização) com chaves e políticas controladas por você no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

A solução de criptografia de disco para Windows é baseada no [Microsoft criptografia de unidade de disco BitLocker](https://technet.microsoft.com/library/cc732774.aspx), e a solução Linux é baseada em [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt).

A solução dá suporte aos seguintes cenários para VMs de IaaS quando elas estão habilitadas no Microsoft Azure:
-   Integração com o Azure Key Vault

-   VMs da camada Standard: VMs IaaS da série A, D, DS, G, GS e assim por diante

-   Habilitando a criptografia em VMs IaaS Windows e Linux

-   Desabilitando a criptografia no sistema operacional e nas unidades de dados para VMs IaaS do Windows

-   Desabilitando a criptografia em unidades de dados para VMs IaaS do Linux

-   Habilitando a criptografia em VMs IaaS que executam o sistema operacional Windows Client

-   Habilitando a criptografia em volumes com caminhos de montagem

-   Habilitando a criptografia em VMs Linux configuradas com a distribuição de disco (RAID) usando o [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Habilitando a criptografia em VMs do Linux usando o [LVM (Gerenciador de volume lógico)](https://msdn.microsoft.com/library/windows/desktop/bb540532) para discos de dados

-   Habilitando a criptografia em VMs do Windows configuradas usando espaços de armazenamento

-   Há suporte para todas as regiões públicas do Azure

A solução não dá suporte aos seguintes cenários, recursos e tecnologia na versão:

-   VMs IaaS de camada básica

-   Desabilitando a criptografia em uma unidade do sistema operacional para VMs IaaS do Linux

-   VMs de IaaS criadas usando o método de criação de VM clássico

-   Integração com o serviço de gerenciamento de chaves local

-   Arquivos do Azure (sistema de arquivos compartilhados), NFS (sistema de arquivos de rede), volumes dinâmicos e VMs do Windows configurados com sistemas RAID baseados em software

## <a name="sql-azure-database-isolation"></a>Isolamento de banco de dados SQL Azure
A Base de Dados SQL é um serviço de bases de dados relacionais na cloud da Microsoft baseado no motor Microsoft SQL Server líder de mercado e com capacidade para processar cargas de trabalho críticas para missões. O SQL Database oferece isolamento de dados previsível em nível de conta, geografia/região e baseado em rede – tudo com administração quase zero.

### <a name="sql-azure-application-model"></a>Modelo de aplicativo SQL Azure

[SQL Azure da Microsoft](../../sql-database/sql-database-single-database-get-started.md) O banco de dados é um serviço de banco de dados relacional baseado em nuvem criado em tecnologias de SQL Server. Ele fornece um serviço de banco de dados altamente disponível, escalonável e multilocatário hospedado pela Microsoft na nuvem.

De uma perspectiva de aplicativo SQL Azure fornece a seguinte hierarquia: cada nível tem confinamento de um para muitos de níveis abaixo.

![Modelo de aplicativo SQL Azure](./media/isolation-choices/azure-isolation-fig10.png)

A conta e a assinatura são Microsoft Azure conceitos de plataforma para associar a cobrança e o gerenciamento.

Servidores lógicos e bancos de dados são conceitos específicos de SQL Azure e são gerenciados usando SQL Azure, interfaces OData e TSQL fornecidas ou por meio do portal de SQL Azure que se integrou ao portal do Azure.

Os servidores de SQL Azure não são instâncias físicas ou de VM, em vez disso, são coleções de bancos de dados, compartilhamento de políticas de segurança e gerenciamento, que são armazenadas em tal como chamado de "mestre lógico".

![SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

Os bancos de dados mestre lógicos incluem:

-   Logons do SQL usados para se conectar ao servidor

-   Regras da firewall

Não há garantia de que as informações relacionadas à cobrança e ao uso para bancos de dados SQL Azure do mesmo servidor lógico estejam na mesma instância física do cluster SQL Azure, em vez disso, os aplicativos devem fornecer o nome do banco de dados de destino ao se conectar.

Do ponto de vista do cliente, um servidor lógico é criado em uma região geográfica gráfica enquanto a criação real do servidor acontece em um dos clusters na região.

### <a name="isolation-through-network-topology"></a>Isolamento por meio da topologia de rede

Quando um servidor lógico é criado e seu nome DNS é registrado, o nome DNS aponta para o endereço chamado "VIP de gateway" no data center específico em que o servidor foi colocado.

Por trás do VIP (endereço IP virtual), temos uma coleção de serviços de gateway sem estado. Em geral, os gateways se envolvem quando há necessidade de coordenação entre várias fontes de dados (banco do dados mestre, banco de dados do usuário, etc.). Os serviços de gateway implementam o seguinte:
-   **Proxy de conexão TDS.** Isso inclui localizar o banco de dados do usuário no cluster de back-end, implementar a sequência de logon e, em seguida, encaminhar os pacotes TDS para o back-end e vice-versa.

-   **Gerenciamento de banco de dados.** Isso inclui a implementação de uma coleção de fluxos de trabalho para operações criar/Alterar/remover banco de dados. As operações de banco de dados podem ser invocadas por meio da detecção de pacotes TDS ou APIs OData explícitas.

-   CRIAR/Alterar/remover operações de logon/usuário

-   Operações de gerenciamento de servidor lógico por meio da API do OData

![Isolamento por meio da topologia de rede](./media/isolation-choices/azure-isolation-fig12.png)

A camada por trás dos gateways é chamada de "back-end". É aí que todos os dados são armazenados de maneira altamente disponível. Cada parte dos dados é indicada para pertencer a uma "partição" ou "unidade de failover", cada um deles com pelo menos três réplicas. As réplicas são armazenadas e replicadas pelo mecanismo de SQL Server e gerenciadas por um sistema de failover geralmente chamados de "malha".

Em geral, o sistema back-end não comunica a saída para outros sistemas como uma precaução de segurança. Isso é reservado para os sistemas na camada de front-end (gateway). Os computadores da camada de gateway têm privilégios limitados nas máquinas de back-end para minimizar a superfície de ataque como um mecanismo de defesa aprofundada.

### <a name="isolation-by-machine-function-and-access"></a>Isolamento por função de máquina e acesso
SQL Azure (é composta de serviços em execução em funções de máquina diferentes. A SQL Azure é dividida no banco de dados de nuvem "backend" e nos ambientes de "front-end" (gateway/gerenciamento), com o princípio geral de tráfego entrando apenas em back-end e não fora. O ambiente de front-end pode se comunicar com o mundo exterior de outros serviços e, em geral, tem apenas permissões limitadas no back-end (o suficiente para chamar os pontos de entrada que precisa invocar).

## <a name="networking-isolation"></a>Isolamento de rede
A implantação do Azure tem várias camadas de isolamento de rede. O diagrama a seguir mostra várias camadas de isolamento de rede que o Azure fornece aos clientes. Essas camadas são nativas na própria plataforma do Azure e recursos definidos pelo cliente. De entrada da Internet, o DDoS do Azure fornece isolamento contra ataques em larga escala contra o Azure. A próxima camada de isolamento são endereços IP públicos definidos pelo cliente (pontos de extremidade), que são usados para determinar qual tráfego pode passar pelo serviço de nuvem para a rede virtual. O isolamento de rede virtual do Azure nativo garante o isolamento completo de todas as outras redes e esse tráfego flui apenas por meio de caminhos e métodos configurados pelo usuário. Esses caminhos e métodos são a próxima camada, em que NSGs, UDR e dispositivos virtuais de rede podem ser usados para criar limites de isolamento para proteger as implantações de aplicativo na rede protegida.

![Isolamento de rede](./media/isolation-choices/azure-isolation-fig13.png)

**Isolamento de tráfego:** Uma [rede virtual](../../virtual-network/virtual-networks-overview.md) é o limite de isolamento de tráfego na plataforma do Azure. As VMs (máquinas virtuais) em uma rede virtual não podem se comunicar diretamente com as VMs em uma rede virtual diferente, mesmo que ambas as redes virtuais sejam criadas pelo mesmo cliente. O isolamento é uma propriedade crítica que garante que as VMs do cliente e a comunicação permaneçam privadas em uma rede virtual.

A [sub-rede](../../virtual-network/virtual-networks-overview.md) oferece uma camada adicional de isolamento com na rede virtual com base no intervalo de IP. Endereços IP na rede virtual, você pode dividir uma rede virtual em várias sub-redes para organização e segurança. As VMs e as instâncias de função de PaaS implementadas em sub-redes (nas mesmas ou em diferentes) dentro de uma VNet podem comunicar entre si sem qualquer configuração adicional. Você também pode configurar o [NSGs (grupo de segurança de rede)](../../virtual-network/virtual-networks-overview.md) para permitir ou negar o tráfego de rede a uma instância de VM com base em regras configuradas na ACL (lista de controle de acesso) do NSG. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede.

## <a name="next-steps"></a>Próximos Passos

- [Opções de isolamento de rede para computadores em redes virtuais do Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Isso inclui o cenário de front-end e back-end clássico em que as máquinas em uma rede de back-end específica ou sub-rede podem permitir que apenas determinados clientes ou outros computadores se conectem a um ponto de extremidade específico com base em uma lista de permissões de endereços IP.

- [Isolamento de computação](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

O Microsoft Azure fornece vários serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias de computação & serviços que podem ser escalados verticalmente e reduzidos automaticamente para atender às necessidades de seu aplicativo ou empresa.

- [Isolamento de armazenamento](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure separa a computação baseada em VM do cliente do armazenamento. Essa separação permite que a computação e o armazenamento sejam dimensionados de forma independente, facilitando o fornecimento de multilocação e isolamento. Portanto, o armazenamento do Azure é executado em hardware separado sem conectividade de rede para a computação do Azure, exceto logicamente. Todas as solicitações são executadas por HTTP ou HTTPS com base na escolha do cliente.
