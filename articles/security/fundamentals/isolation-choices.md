---
title: Isolamento na Nuvem Pública Azul  Microsoft Docs
description: Saiba como o Azure proporciona isolamento contra utilizadores maliciosos e não maliciosos e oferece várias opções de isolamento a arquitetos.
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
ms.openlocfilehash: c6e74e7992326d2a4b8fe24510742422b005c2e2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280316"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolamento na Nuvem Pública Azure
O Azure permite-lhe executar aplicações e máquinas virtuais (VMs) em infraestruturas físicas partilhadas. Uma das principais motivações económicas para executar aplicações num ambiente de nuvem é a capacidade de distribuir o custo dos recursos partilhados entre vários clientes. Esta prática de multiarrendamento melhora a eficiência ao multiplexing recursos entre clientes diferentes a baixos custos. Infelizmente, também introduz o risco de partilhar servidores físicos e outros recursos de infraestrutura para executar as suas aplicações sensíveis e VMs que podem pertencer a um utilizador arbitrário e potencialmente malicioso.

Este artigo descreve como o Azure proporciona isolamento contra utilizadores maliciosos e não maliciosos e serve como um guia para arquitetar soluções de nuvem, oferecendo várias opções de isolamento aos arquitetos.

## <a name="tenant-level-isolation"></a>Isolamento nível de inquilino
Um dos principais benefícios da computação em nuvem é o conceito de uma infraestrutura comum partilhada em inúmeros clientes simultaneamente, levando a economias de escala. Este conceito chama-se multi-arrendamento. A Microsoft trabalha continuamente para garantir que a arquitetura multi-arrendatária do Microsoft Cloud Azure suporta padrões de segurança, confidencialidade, privacidade, integridade e disponibilidade.

Na área de trabalho ativada na nuvem, um inquilino pode ser definido com um cliente ou uma organização que possui e gere uma instância específica do referido serviço em nuvem. Com a plataforma de identidade fornecida pelo Microsoft Azure, um inquilino é simplesmente uma instância dedicada do Azure Ative Directory (Azure AD) que a sua organização recebe e possui quando se inscreve num serviço de cloud da Microsoft.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Tal como um edifício de escritórios empresariais é um recurso seguro específico apenas para a sua organização, um diretório do Azure AD também foi concebido para ser um recurso com utilização segura apenas para a sua organização. A arquitetura do Azure AD impede que os dados do cliente e as informações de identidade se misturem. Isto significa que os utilizadores e administradores de um diretório do Azure AD não podem, acidental ou intencionalmente, aceder aos dados de outro diretório.

### <a name="azure-tenancy"></a>Arrendamento Azure
O arrendamento Azure (Assinatura Azure) refere-se a uma relação "cliente/faturação" e a um [inquilino](../../active-directory/develop/quickstart-create-new-tenant.md) único no [Azure Ative Directory.](../../active-directory/fundamentals/active-directory-whatis.md) O isolamento ao nível dos inquilinos no Microsoft Azure é alcançado utilizando o Azure Ative Directory e [os controlos baseados em papéis](../../role-based-access-control/overview.md) oferecidos por ele. Cada subscrição azure está associada a um diretório azure Ative Directory (AD).

Utilizadores, grupos e aplicações desse diretório podem gerir recursos na subscrição do Azure. Pode atribuir estes direitos de acesso utilizando o portal Azure, as ferramentas de linha de comando Azure e as APIs de Gestão Azure. Um inquilino da AD Azure está logicamente isolado usando limites de segurança para que nenhum cliente possa aceder ou comprometer coinquilinos, maliciosamente ou acidentalmente. O Azure AD funciona em servidores "bare metal" isolados num segmento de rede segregado, onde a filtragem de pacotes de nível de anfitrião e o bloqueio de firewall do Windows Firewall bloqueiam ligações indesejadas e tráfego.

- O acesso aos dados em AD Azure requer a autenticação do utilizador através de um serviço de token de segurança (STS). A informação sobre a existência do utilizador, o estado habilitado e a função são utilizadas pelo sistema de autorização para determinar se o acesso solicitado ao arrendatário-alvo está autorizado para este utilizador nesta sessão.

![Arrendamento Azure](./media/isolation-choices/azure-isolation-fig1.png)


- Os inquilinos são contentores discretos e não há relação entre estes.

- Não há acesso entre inquilinos a menos que o administrador inquilino o conceda através da federação ou do fornecimento de contas de utilizadores de outros inquilinos.

- O acesso físico aos servidores que compõem o serviço Azure AD e o acesso direto aos sistemas back-end da Azure AD, é restrito.

- Os utilizadores da AD Azure não têm acesso a ativos físicos ou localizações, pelo que não é possível contornar os controlos lógicos da política rbac indicados a seguir.

Para diagnósticos e necessidades de manutenção, é necessário e utilizado um modelo operacional que empregue um sistema de elevação de privilégios just-in-time. A Azure AD Privileged Identity Management (PIM) introduz o conceito de administrador elegível. [Os administradores elegíveis](../../active-directory/privileged-identity-management/pim-configure.md) devem ser utilizadores que precisam de acesso privilegiado de vez em quando, mas não todos os dias. A função está inativa até que o utilizador precise de acesso. Nessa altura, o utilizador realiza um processo de ativação e torna-se num administrador ativo durante uma quantidade pré-determinada de tempo.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

O Azure Ative Directory acolhe cada inquilino no seu próprio contentor protegido, com políticas e permissões para e dentro do contentor exclusivamente propriedade e gerido pelo inquilino.

O conceito de contentores de inquilinos está profundamente enraizado no serviço de diretório em todas as camadas, desde portais até armazenamento persistente.

Mesmo quando os metadados de vários inquilinos do Azure Ative Directory são armazenados no mesmo disco físico, não há qualquer relação entre os contentores que não o definido pelo serviço de diretório, que por sua vez é ditado pelo administrador inquilino.

### <a name="azure-role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções Azure (RBAC)
[O Azure Role-Based Access Control (RBAC) ajuda-o](../../role-based-access-control/overview.md) a partilhar vários componentes disponíveis dentro de uma subscrição do Azure, fornecendo uma gestão de acesso de grãos finos para o Azure. O Azure RBAC permite-lhe segregar os deveres dentro da sua organização e conceder acesso com base no que os utilizadores precisam para desempenhar os seus trabalhos. Em vez de dar permissões sem restrições a todos na subscrição ou recursos do Azure, só pode permitir certas ações.

O Azure RBAC tem três funções básicas aplicáveis a todos os tipos de recursos:

- **O proprietário** tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outros.

- **O colaborador** pode criar e gerir todos os tipos de recursos Do Azure, mas não pode dar acesso a outros.

- **O leitor** pode ver os recursos azure existentes.

![Controlo de Acesso baseado em funções azure](./media/isolation-choices/azure-isolation-fig3.png)

As restantes funções rBAC no Azure permitem a gestão de recursos específicos do Azure. Por exemplo, a função De Colaborador da Máquina Virtual permite ao utilizador criar e gerir máquinas virtuais. Não lhes dá acesso à Rede Virtual Azure ou à sub-rede a que a máquina virtual se liga.

[As funções integradas rBAC](../../role-based-access-control/built-in-roles.md) listam as funções disponíveis no Azure. Especifica as operações e o âmbito que cada papel incorporado concede aos utilizadores. Se procura definir os seus próprios papéis para ainda mais controlo, veja como construir [papéis personalizados no Azure RBAC](../../role-based-access-control/custom-roles.md).

Algumas outras capacidades para o Diretório Ativo Azure incluem:
- A Azure AD permite aplicações SSO para SaaS, independentemente do local onde estejam hospedadas. Algumas aplicações estão federadas com o Azure AD e outras utilizam SSO com palavra-passe. As aplicações federadas também podem suportar o fornecimento de utilizadores e [a abóbada de senhas.](https://www.techopedia.com/definition/31415/password-vault)

- O acesso aos dados do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado através da autenticação. Cada conta de armazenamento tem uma chave primária[(chave de conta](../../storage/common/storage-create-storage-account.md)de armazenamento, ou SAK) e uma chave secreta secundária (a assinatura de acesso partilhado, ou SAS).

- A Azure AD fornece identidade como serviço através da federação, utilizando serviços da [Federação de Diretórios Ativos,](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md)sincronização e replicação com diretórios no local.

- [A Autenticação Multi-Factor Azure](../../active-directory/authentication/multi-factor-authentication.md) é o serviço de autenticação multifactor que exige que os utilizadores verifiquem os registos através de uma aplicação móvel, chamada telefónica ou mensagem de texto. Pode ser usado com AD Azure para ajudar a proteger recursos no local com o servidor de autenticação multi-factor Azure, e também com aplicações e diretórios personalizados usando o SDK.

- [O Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite-lhe juntar as máquinas virtuais Azure a um domínio de Diretório Ativo sem implementar controladores de domínio. Pode iniciar sessão nestas máquinas virtuais com as suas credenciais de Diretório Ativo corporativo e administrar máquinas virtuais unidas ao domínio, utilizando a Política de Grupo para impor as bases de segurança em todas as suas máquinas virtuais Azure.

- O [Azure Ative Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) oferece um serviço de gestão de identidade global altamente disponível para aplicações viradas para o consumidor que se elevou a centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os seus consumidores podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis utilizando as suas contas sociais existentes ou criando credenciais.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolamento dos Administradores da Microsoft e eliminação de dados
A Microsoft toma medidas fortes para proteger os seus dados de acesso ou utilização inadequados por pessoas não autorizadas. Estes processos e controlos operacionais são apoiados pelos [Termos dos Serviços Online,](https://aka.ms/Online-Services-Terms)que oferecem compromissos contratuais que regem o acesso aos seus dados.

-   Os engenheiros da Microsoft não têm acesso padrão aos seus dados na nuvem. Em vez disso, só lhes é concedido acesso, sob supervisão da gestão, quando necessário. Este acesso é cuidadosamente controlado e registado, e revogado quando já não é necessário.

-   A Microsoft pode contratar outras empresas para prestar serviços limitados em seu nome. Os subempreiteiros só podem aceder aos dados dos clientes para prestar os serviços para os quais os contratámos para prestar, e estão proibidos de os utilizar para qualquer outro fim. Além disso, estão contratualmente obrigados a manter a confidencialidade das informações dos nossos clientes.

Os serviços empresariais com certificações auditadas, tais como ISO/IEC 27001, são regularmente verificados pela Microsoft e por empresas de auditoria acreditadas, que realizam auditorias de amostras para atestar esse acesso, apenas para fins comerciais legítimos. Pode sempre aceder aos dados dos seus próprios clientes a qualquer momento e por qualquer motivo.

Se eliminar quaisquer dados, o Microsoft Azure elimina os dados, incluindo quaisquer cópias em cache ou cópias de cópias de reserva. No caso dos serviços no âmbito, essa eliminação ocorrerá no prazo de 90 dias após o fim do período de retenção. (Os serviços no âmbito são definidos na secção termos de processamento de dados dos [nossos Termos de Serviços Online](https://aka.ms/Online-Services-Terms).)

Se uma unidade de disco utilizada para armazenamento sofrer uma falha de hardware, é [apagada ou destruída](https://microsoft.com/trustcenter/privacy/you-own-your-data) de forma segura antes de a Microsoft a devolver ao fabricante para substituição ou reparação. Os dados da unidade são substituídos para garantir que os dados não possam ser recuperados por qualquer meio.

## <a name="compute-isolation"></a>Isolamento computacional
O Microsoft Azure fornece vários serviços de computação baseados na nuvem que incluem uma ampla seleção de instâncias e serviços computacionais que podem escalar para cima e para baixo automaticamente para atender às necessidades da sua aplicação ou empresa. Estes compute instance e serviço oferecem isolamento a vários níveis para garantir dados sem sacrificar a flexibilidade na configuração que os clientes exigem.

### <a name="isolated-virtual-machine-sizes"></a>Tamanhos de máquina virtual isolada

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Anfitriões dedicados
Além dos anfitriões isolados descritos na secção anterior, o Azure também oferece anfitriões dedicados. Anfitriões dedicados em Azure é um serviço que fornece servidores físicos que podem hospedar uma ou mais máquinas virtuais, e que são dedicados a uma única subscrição azure. Os anfitriões dedicados fornecem isolamento de hardware ao nível do servidor físico. Nenhum outro VMs será colocado nos seus anfitriões. Os anfitriões dedicados são implantados nos mesmos centros de dados e partilham a mesma rede e infraestruturas de armazenamento subjacentes que outros hospedeiros não isolados. Para mais informações, consulte a visão geral detalhada dos [anfitriões dedicados ao Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Isolamento hiper-V e Root OS entre VM raiz e VMs convidados
A plataforma computacional da Azure baseia-se na virtualização da máquina — o que significa que todo o código do cliente executa numa máquina virtual Hyper-V. Em cada nó Azure (ou ponto final da rede), existe um Hipervisor que passa diretamente sobre o hardware e divide um nó num número variável de Máquinas Virtuais Convidadas (VMs).


![Isolamento hiper-V e Root OS entre VM raiz e VMs convidados](./media/isolation-choices/azure-isolation-fig4.jpg)


Cada nó também tem um VM raiz especial, que executa o Os hospedeiro. Uma fronteira crítica é o isolamento da raiz VM dos VMs convidados e dos VMs convidados uns dos outros, geridos pelo hipervisor e pela raiz oss. O emparelhamento hipervisor/root OS aproveita as décadas de experiência de segurança do sistema operativo da Microsoft e a aprendizagem mais recente do Hyper-V da Microsoft, para proporcionar um forte isolamento dos VMs convidados.

A plataforma Azure utiliza um ambiente virtualizado. As instâncias de utilizador funcionam como máquinas virtuais autónomas que não têm acesso a um servidor de hospedamento físico.

O hipervisor Azure age como um micro-kernel e passa todos os pedidos de acesso ao hardware de máquinas virtuais para o anfitrião para processamento usando uma interface de memória partilhada chamada VMBus. Isto impede os utilizadores de obterem acesso de leitura/escrita/execução não processado ao sistema e atenua o risco de partilha de recursos do sistema.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Algoritmo avançado de colocação de VM e proteção contra ataques de canais laterais
Qualquer ataque transversal envolve dois passos: colocar um VM controlado pelo adversário no mesmo hospedeiro que um dos VMs da vítima, e, em seguida, violar o limite de isolamento para roubar informações sensíveis da vítima ou afetar o seu desempenho por ganância ou vandalismo. O Microsoft Azure fornece proteção em ambas as etapas utilizando um algoritmo avançado de colocação de VM e proteção de todos os ataques de canais laterais conhecidos, incluindo VMs vizinhos barulhentos.

### <a name="the-azure-fabric-controller"></a>O controlador de tecido azure
O Controlador de Tecidos Azure é responsável pela atribuição de recursos de infraestrutura a cargas de trabalho dos inquilinos, e gere comunicações unidirecionais do hospedeiro a máquinas virtuais. O algoritmo de colocação vm do controlador de tecido Azure é altamente sofisticado e quase impossível de prever como nível de hospedeiro físico.

![O controlador de tecido azure](./media/isolation-choices/azure-isolation-fig5.png)

O hipervisor Azure impõe a separação de memória e processo entre máquinas virtuais, e encaminha de forma segura o tráfego da rede para os inquilinos do SO convidados. Isto elimina a possibilidade de ataque e canal lateral a nível VM.

Em Azure, a raiz VM é especial: executa um sistema operativo endurecido chamado o SISTEMA de raiz que acolhe um agente de tecido (FA). As FAs são usadas por sua vez para gerir agentes convidados (GA) dentro de OSes convidados em VMs de clientes. As FAs também gerem os nódosos de armazenamento.

A coleção de hipervisor Azure, raiz OS/FA e VMs/GAs do cliente compreende um nó computacional. As FAs são geridas por um controlador de tecido (FC), que existe fora dos nós de computação e armazenamento (os clusters de computação e armazenamento são geridos por FCs separados). Se um cliente atualizar o ficheiro de configuração da sua aplicação enquanto está em execução, o FC comunica com a FA, que depois contacta os GAs, que notificam a aplicação da alteração de configuração. Em caso de falha de hardware, o FC encontrará automaticamente o hardware disponível e reiniciará o VM lá.

![Controlador de tecido azure](./media/isolation-choices/azure-isolation-fig6.jpg)

A comunicação de um controlador de tecido a um agente é unidirecional. O agente implementa um serviço protegido pela SSL que apenas responde aos pedidos do controlador. Não pode iniciar ligações ao controlador ou a outros nódos internos privilegiados. O FC trata todas as respostas como se não fossem fidedignas.


![Controlador de tecido](./media/isolation-choices/azure-isolation-fig7.png)

O isolamento estende-se desde o Root VM dos Guest VMs, e os VMs convidados uns dos outros. Os nódosos computacionais também estão isolados dos nódosos de armazenamento para uma maior proteção.


O hipervisor e o hospedeiro osso fornecem pacote de rede - filtros para ajudar a assegurar que máquinas virtuais não confiáveis não podem gerar tráfego falsificado ou receber tráfego não endereçado a eles, tráfego direto para pontos finais de infraestrutura protegidos, ou enviar/receber tráfego de transmissão inadequado.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Regras adicionais configuradas pelo controlador de tecido para isolar vm
Por padrão, todo o tráfego é bloqueado quando uma máquina virtual é criada, e em seguida, o controlador de tecido sumis o filtro do pacote para adicionar regras e exceções para permitir o tráfego autorizado.

Há duas categorias de regras que são programadas:

-   Regras de **configuração da máquina ou de infraestrutura:** Por padrão, toda a comunicação está bloqueada. Existem exceções para permitir que uma máquina virtual envie e receba tráfego DHCP e DNS. As máquinas virtuais também podem enviar tráfego para a internet "pública" e enviar tráfego para outras máquinas virtuais dentro da mesma Rede Virtual Azure e do servidor de ativação de SO. A lista de destinos de saída permitidos das máquinas virtuais não inclui subredes de router Azure, gestão Azure e outras propriedades da Microsoft.

-   **Ficheiro de configuração de funções:** Isto define as Listas de Controlo de Acesso de Entrada (ACLs) com base no modelo de serviço do arrendatário.

### <a name="vlan-isolation"></a>Isolamento de VLAN
Há três VLANs em cada cluster:

![Isolamento de VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)


-   O VLAN principal - interliga nódosos de clientes não confiáveis

-   O FC VLAN – contém FCs fidedignos e sistemas de apoio

-   O dispositivo VLAN – contém rede fidedigna e outros dispositivos de infraestrutura

A comunicação é permitida desde o VLAN FC até ao VLAN principal, mas não pode ser iniciada desde o VLAN principal até ao VLAN FC. A comunicação também está bloqueada desde o VLAN principal até ao dispositivo VLAN. Isto garante que mesmo que um nó que execute o código do cliente esteja comprometido, não pode atacar nós no FC ou no dispositivo VLANs.

## <a name="storage-isolation"></a>Isolamento de armazenamento
### <a name="logical-isolation-between-compute-and-storage"></a>Isolamento lógico entre a computação e o armazenamento
Como parte do seu design fundamental, o Microsoft Azure separa a computação baseada em VM do armazenamento. Esta separação permite que a computação e o armazenamento escalonem de forma independente, facilitando a disponibilização de vários arrendamentos e isolamentos.

Portanto, o Azure Storage funciona em hardware separado sem conectividade de rede com a Azure Compute, exceto logicamente. Isto significa que quando um disco virtual é criado, o espaço do disco não é atribuído para toda a sua capacidade. Em vez disso, é criada uma tabela que mapeia endereços no disco virtual para áreas no disco físico e essa tabela está inicialmente vazia. **A primeira vez que um cliente escreve dados no disco virtual, o espaço no disco físico é atribuído, e um ponteiro para ele é colocado na tabela.**
### <a name="isolation-using-storage-access-control"></a>Isolamento Usando o controlo de acesso de armazenamento
**O Controlo de Acesso no Armazenamento Azure** tem um modelo de controlo de acesso simples. Cada subscrição do Azure pode criar uma ou mais Contas de Armazenamento. Cada Conta de Armazenamento tem uma única chave secreta que é usada para controlar o acesso a todos os dados dessa Conta de Armazenamento.

![Isolamento Usando o controlo de acesso de armazenamento](./media/isolation-choices/azure-isolation-fig9.png)

O acesso aos dados de **armazenamento azure (incluindo tabelas)** pode ser controlado através de um símbolo [SAS (Assinatura](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) de Acesso Partilhado), que concede acesso acessível. O SAS é criado através de um modelo de consulta (URL), assinado com o [SAK (Chave da Conta](https://msdn.microsoft.com/library/azure/ee460785.aspx)de Armazenamento) . Esse [URL assinado](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) pode ser dado a outro processo (isto é, delegado), que pode então preencher os detalhes da consulta e fazer o pedido do serviço de armazenamento. Um SAS permite-lhe conceder acesso baseado no tempo aos clientes sem revelar a chave secreta da conta de armazenamento.

O SAS significa que podemos conceder permissões limitadas a um cliente, a objetos na nossa conta de armazenamento por um período de tempo determinado e com um conjunto especificado de permissões. Podemos conceder estas permissões limitadas sem ter que partilhar as chaves de acesso da sua conta.

### <a name="ip-level-storage-isolation"></a>Isolamento de armazenamento de nível IP
Pode estabelecer firewalls e definir um intervalo de endereçoIP para os seus clientes de confiança. Com uma gama de endereços IP, apenas os clientes que possuem um endereço IP dentro da gama definida podem ligar-se ao [Armazenamento Azure](../../storage/blobs/security-recommendations.md).

Os dados de armazenamento IP podem ser protegidos contra utilizadores não autorizados através de um mecanismo de rede que é usado para alocar um túnel de tráfego dedicado ou dedicado ao armazenamento ip.

### <a name="encryption"></a>Encriptação
O Azure oferece os seguintes tipos de encriptação para proteger dados:
-   Encriptação em trânsito

-   Encriptação inativa

#### <a name="encryption-in-transit"></a>Encriptação em Trânsito
A encriptação em trânsito é um mecanismo de proteção de dados quando é transmitido através das redes. Com o Armazenamento Azure, pode proteger os dados utilizando:

-   [Encriptação ao nível do transporte](../../storage/blobs/security-recommendations.md), como HTTPS quando transfere dados para ou para fora do Armazenamento Azure.

-   [Encriptação por fio](../../storage/blobs/security-recommendations.md), como encriptação SMB 3.0 para ações do Ficheiro Azure.

-   [Encriptação do lado do cliente,](../../storage/blobs/security-recommendations.md)para encriptar os dados antes de ser transferido para o armazenamento e desencriptar os dados após a sua transferência para fora do armazenamento.

#### <a name="encryption-at-rest"></a>Encriptação em Repouso
Para muitas organizações, a [encriptação](isolation-choices.md) de dados em repouso é um passo obrigatório para a privacidade dos dados, conformidade e soberania de dados. Existem três funcionalidades Azure que fornecem encriptação de dados que está "em repouso":

-   [A encriptação](../../storage/blobs/security-recommendations.md) do Serviço de Armazenamento permite-lhe solicitar que o serviço de armazenamento criptografe automaticamente os dados ao escrevê-lo no Armazenamento Azure.

-   [A encriptação do lado do cliente](../../storage/blobs/security-recommendations.md) também fornece a funcionalidade de encriptação em repouso.

-   [A Encriptação do Disco Azure](../azure-security-disk-encryption-overview.md) permite-lhe encriptar os discos e discos de dados do SO utilizados por uma máquina virtual IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[A encriptação](../azure-security-disk-encryption-overview.md) do disco azure para máquinas virtuais (VMs) ajuda-o a resolver os requisitos de segurança organizacional e conformidade encriptando os seus discos VM (incluindo boot e discos de dados) com chaves e políticas que controla no [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/).

A solução de encriptação do disco para windows baseia-se na [encriptação do Microsoft BitLocker Drive](https://technet.microsoft.com/library/cc732774.aspx), e a solução Linux [baseia-se na dm-cripta](https://en.wikipedia.org/wiki/Dm-crypt).

A solução suporta os seguintes cenários para os VMs IaaS quando estão ativados no Microsoft Azure:
-   Integração com cofre chave Azure

-   VMs de nível padrão: A, D, DS, G, GS, e assim por diante, série VMs

-   Ativar a encriptação nos VMs Windows e Linux IaaS

-   Desativar a encriptação no OS e unidades de dados para VMs Do Windows IaaS

-   Desativar a encriptação em unidades de dados para VMs Linux IaaS

-   Ativar a encriptação em VMs IaaS que estão executando os clientes do Windows OS

-   Habilitar a encriptação em volumes com caminhos de montagem

-   Ativar a encriptação em VMs Linux que são configurados com striping de disco (RAID) usando [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Permitir a encriptação em VMs Linux utilizando [LVM (Logical Volume Manager)](https://msdn.microsoft.com/library/windows/desktop/bb540532) para discos de dados

-   Ativar a encriptação em VMs do Windows que são configurados usando espaços de armazenamento

-   Todas as regiões públicas de Azure são apoiadas

A solução não suporta os seguintes cenários, funcionalidades e tecnologia no lançamento:

-   VMs de nível básico IaaS

-   Desativação da encriptação em uma unidade de SO para VMs Linux IaaS

-   VMs IaaS que são criados usando o método clássico de criação VM

-   Integração com o seu serviço de gestão de chaves no local

-   Ficheiros Azure (sistema de ficheiros partilhados), Sistema de Ficheiros de Rede (NFS), volumes dinâmicos e VMs do Windows que estão configurados com sistemas RAID baseados em software

## <a name="sql-azure-database-isolation"></a>Isolamento da base de dados SQL Azure
A Base de Dados SQL é um serviço de bases de dados relacionais na cloud da Microsoft baseado no motor Microsoft SQL Server líder de mercado e com capacidade para processar cargas de trabalho críticas para missões. A Base de Dados SQL oferece um isolamento previsível de dados ao nível da conta, geografia/região baseada em networking, tudo com administração quase nula.

### <a name="sql-azure-application-model"></a>Modelo de aplicação SQL Azure

[Microsoft SQL Azure](../../sql-database/sql-database-single-database-get-started.md) A base de dados é um serviço de base de dados relacional baseado na nuvem construído em tecnologias SQL Server. Fornece um serviço de base de dados altamente disponível, escalável e multi-inquilinos hospedado pela Microsoft na nuvem.

Do ponto de vista da aplicação, o SQL Azure fornece a seguinte hierarquia: Cada nível tem uma a muita contenção de níveis abaixo.

![Modelo de aplicação SQL Azure](./media/isolation-choices/azure-isolation-fig10.png)

A conta e a subscrição são conceitos da plataforma Microsoft Azure para associados à faturação e gestão.

Servidores lógicos e bases de dados são conceitos específicos do SQL Azure e são geridos através da utilização do SQL Azure, forneceu interfaces OData e TSQL ou através do portal SQL Azure que se integrou no portal Azure.

Os servidores SQL Azure não são instâncias físicas ou VM, em vez disso são coleções de bases de dados, partilha de políticas de gestão e segurança, que são armazenadas na chamada base de dados "master lógica".

![SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

As bases de dados principais lógicas incluem:

-   SQL logins usados para ligar ao servidor

-   Regras da firewall

Não é garantido que a faturação e informações relacionadas com o uso das bases de dados Do SQL Azure do mesmo servidor lógico estejam na mesma instância física no cluster SQL Azure, em vez disso as aplicações devem fornecer o nome da base de dados alvo ao ligar.

Do ponto de vista do cliente, um servidor lógico é criado numa região geográfica enquanto a criação real do servidor acontece num dos clusters da região.

### <a name="isolation-through-network-topology"></a>Isolamento através da Topologia da Rede

Quando um servidor lógico é criado e o seu nome DNS é registado, o nome DNS aponta para o chamado endereço "Gateway VIP" no centro de dados específico onde o servidor foi colocado.

Atrás do VIP (endereço IP virtual), temos uma coleção de serviços de gateway apátridas. Em geral, os gateways envolvem-se quando é necessária uma coordenação entre várias fontes de dados (base de dados principal, base de dados do utilizador, etc.). Os serviços gateway implementam o seguinte:
-   **Procuração de ligação TDS.** Isto inclui localizar a base de dados do utilizador no cluster backend, implementar a sequência de login e, em seguida, encaminhar os pacotes DeTS para o backend e back.

-   **Gestão de bases de dados.** Isto inclui a implementação de uma recolha de fluxos de trabalho para eexecutar operações de base de dados CREATE/ALTER/DROP. As operações de base de dados podem ser invocadas por pacotes TDS farejadores ou APIs oData explícitos.

-   OPERAÇÕES DE INÍCIO DE Sessão/Utilizador CREATE/ALTER/DROP

-   Operações lógicas de gestão de servidores via OData API

![Isolamento através da Topologia da Rede](./media/isolation-choices/azure-isolation-fig12.png)

O nível atrás dos portões chama-se "back-end". É aqui que todos os dados são armazenados de forma altamente disponível. Cada pedaço de dados pertence a uma "unidade de partição" ou "failover", cada um deles com pelo menos três réplicas. As réplicas são armazenadas e replicadas pelo motor SQL Server e geridas por um sistema de failover muitas vezes referido como "tecido".

Geralmente, o sistema back-end não comunica a saída para outros sistemas como uma precaução de segurança. Isto é reservado aos sistemas no nível frontal (gateway). As máquinas de nível gateway têm privilégios limitados nas máquinas traseiras para minimizar a superfície de ataque como um mecanismo de defesa em profundidade.

### <a name="isolation-by-machine-function-and-access"></a>Isolamento por Função e Acesso da Máquina
O SQL Azure (é composto por serviços em funcionamento em diferentes funções de máquina. O SQL Azure está dividido em ambientes "backend" cloud database e "front-end" (Gateway/Management), com o princípio geral do tráfego a entrar apenas no back-end e não fora. O ambiente frontal pode comunicar com o mundo exterior de outros serviços e, em geral, tem apenas permissões limitadas no back-end (o suficiente para chamar os pontos de entrada que precisa invocar).

## <a name="networking-isolation"></a>Isolamento em rede
A implantação do Azure tem várias camadas de isolamento de rede. O diagrama seguinte mostra várias camadas de isolamento de rede que o Azure fornece aos clientes. Estas camadas são nativas na própria plataforma Azure e funcionalidades definidas pelo cliente. A entrada da Internet, o Azure DDoS proporciona isolamento contra ataques em larga escala contra o Azure. A próxima camada de isolamento são os endereços IP públicos definidos pelo cliente (pontos finais), que são usados para determinar que tráfego pode passar através do serviço de nuvem para a rede virtual. O isolamento da rede virtual Native Azure garante o isolamento total de todas as outras redes, e que o tráfego flui apenas através de caminhos e métodos configurados pelo utilizador. Estes caminhos e métodos são a próxima camada, onde nsGs, UDR e aparelhos virtuais de rede podem ser usados para criar limites de isolamento para proteger as implementações de aplicações na rede protegida.

![Isolamento em rede](./media/isolation-choices/azure-isolation-fig13.png)

**Isolamento de trânsito:** Uma [rede virtual](../../virtual-network/virtual-networks-overview.md) é o limite de isolamento de tráfego na plataforma Azure. As máquinas virtuais (VMs) numa rede virtual não podem comunicar diretamente com VMs numa rede virtual diferente, mesmo que ambas as redes virtuais sejam criadas pelo mesmo cliente. O isolamento é uma propriedade crítica que garante aos Clientes VMs e a comunicação permanece privada dentro de uma rede virtual.

[A Subnet](../../virtual-network/virtual-networks-overview.md) oferece uma camada adicional de isolamento com a rede virtual com base na gama IP. Endereços IP na rede virtual, pode dividir uma rede virtual em várias subredes para organização e segurança. As VMs e as instâncias de função de PaaS implementadas em sub-redes (nas mesmas ou em diferentes) dentro de uma VNet podem comunicar entre si sem qualquer configuração adicional. Também pode configurar o grupo de segurança da [rede (NSGs)](../../virtual-network/virtual-networks-overview.md) para permitir ou negar o tráfego de rede a uma instância VM com base em regras configuradas na lista de controlo de acesso (ACL) do NSG. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede.

## <a name="next-steps"></a>Próximos Passos

- Saiba mais sobre as opções de isolamento da [rede para máquinas em redes virtuais Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Isto inclui o cenário clássico de frente e back-end onde as máquinas de uma determinada rede ou subrede de back-end só podem permitir que certos clientes ou outros computadores se conectem a um determinado ponto final com base numa lista de endereços IP.

- Conheça o isolamento virtual da [máquina em Azure.](../../virtual-machines/windows/isolation.md) A Azure Compute oferece tamanhos de máquinas virtuais que estão isolados a um tipo de hardware específico e dedicados a um único cliente.
