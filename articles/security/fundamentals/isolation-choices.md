---
title: Isolamento na Nuvem Pública do Azure Microsoft Docs
description: Saiba como a Azure proporciona isolamento contra utilizadores maliciosos e não maliciosos e oferece várias opções de isolamento aos arquitetos.
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
ms.openlocfilehash: fa2025fa31ac960eb6c61d03bafd582de4f0e55c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410582"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolamento na Nuvem Pública de Azure

O Azure permite-lhe executar aplicações e máquinas virtuais (VMs) em infraestruturas físicas partilhadas. Uma das principais motivações económicas para executar aplicações em ambiente nublado é a capacidade de distribuir o custo dos recursos partilhados entre vários clientes. Esta prática de multi-arrendamento melhora a eficiência através da multiplexing recursos entre clientes diferentes a baixos custos. Infelizmente, também introduz o risco de partilhar servidores físicos e outros recursos de infraestrutura para executar as suas aplicações sensíveis e VMs que podem pertencer a um utilizador arbitrário e potencialmente malicioso.

Este artigo descreve como a Azure proporciona isolamento contra utilizadores maliciosos e não maliciosos e serve de guia para a arquiteta de soluções em nuvem, oferecendo várias opções de isolamento aos arquitetos.

## <a name="tenant-level-isolation"></a>Isolamento nível de inquilino

Um dos principais benefícios da computação em nuvem é o conceito de uma infraestrutura comum partilhada em vários clientes simultaneamente, levando a economias de escala. Este conceito chama-se multi-arrendamento. A Microsoft trabalha continuamente para garantir que a arquitetura multi-inquilino do Microsoft Cloud Azure suporta padrões de segurança, confidencialidade, privacidade, integridade e disponibilidade.

Na área de trabalho ativada na nuvem, um inquilino pode ser definido com um cliente ou uma organização que possui e gere uma instância específica do referido serviço em nuvem. Com a plataforma de identidade fornecida pela Microsoft Azure, um inquilino é simplesmente uma instância dedicada do Azure Ative Directory (Azure AD) que a sua organização recebe e possui quando se inscreve para um serviço de cloud da Microsoft.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Tal como um edifício de escritórios empresariais é um recurso seguro específico apenas para a sua organização, um diretório do Azure AD também foi concebido para ser um recurso com utilização segura apenas para a sua organização. A arquitetura do Azure AD impede que os dados do cliente e as informações de identidade se misturem. Isto significa que os utilizadores e administradores de um diretório do Azure AD não podem, acidental ou intencionalmente, aceder aos dados de outro diretório.

### <a name="azure-tenancy"></a>Arrendamento Azure

Azure tenancy (Azure Subscription) refere-se a uma relação "cliente/faturação" e a um [inquilino](../../active-directory/develop/quickstart-create-new-tenant.md) único no [Azure Ative Directory](../../active-directory/fundamentals/active-directory-whatis.md). O isolamento do nível do inquilino no Microsoft Azure é alcançado usando o Azure Ative Directory e [os controlos baseados em funções oferecidos](../../role-based-access-control/overview.md) por ele. Cada subscrição do Azure está associada a um diretório Azure Ative (AD).

Utilizadores, grupos e aplicações desse diretório podem gerir recursos na subscrição do Azure. Pode atribuir estes direitos de acesso utilizando o portal Azure, as ferramentas da linha de comando Azure e as APIs de Gestão Azure. Um inquilino da AD Azure é logicamente isolado usando limites de segurança para que nenhum cliente possa aceder ou comprometer os coinquilinos, quer maliciosamente quer acidentalmente. O Azure AD funciona em servidores "de metal nu" isolados num segmento de rede segregado, onde a filtragem de pacotes de nível de hospedeiro e o Windows Firewall bloqueiam ligações e tráfego indesejados.

- O acesso aos dados em Azure AD requer a autenticação do utilizador através de um serviço de fichas de segurança (STS). Informações sobre a existência do utilizador, estado habilitado e função são utilizadas pelo sistema de autorização para determinar se o acesso solicitado ao inquilino-alvo é autorizado para este utilizador nesta sessão.

![Arrendamento Azure](./media/isolation-choices/azure-isolation-fig1.png)

- Os inquilinos são contentores discretos e não há relação entre estes.

- Não há acesso entre inquilinos a menos que o administrador inquilino o conceda através da federação ou provisionando contas de utilizadores de outros inquilinos.

- O acesso físico aos servidores que compõem o serviço AZure AD e o acesso direto aos sistemas back-end da Azure AD é restrito.

- Os utilizadores de AD Azure não têm acesso a ativos ou locais físicos, pelo que não lhes é possível contornar os controlos de política lógicos da RBAC indicados a seguir.

Para diagnósticos e necessidades de manutenção, é necessário e utilizado um modelo operacional que utilize um sistema de elevação de privilégios just-in-time. A Azure AD Privileged Identity Management (PIM) introduz o [Eligible admins](../../active-directory/privileged-identity-management/pim-configure.md) conceito de administrador elegível. A função está inativa até que o utilizador precise de acesso. Nessa altura, o utilizador realiza um processo de ativação e torna-se num administrador ativo durante uma quantidade pré-determinada de tempo.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

O Azure Ative Directory acolhe cada inquilino no seu próprio contentor protegido, com políticas e permissões para e dentro do contentor exclusivamente propriedade e gerido pelo arrendatário.

O conceito de contentores de inquilinos está profundamente enraizado no serviço de diretório em todas as camadas, desde portais até ao armazenamento persistente.

Mesmo quando os metadados de vários inquilinos do Azure Ative Directory são armazenados no mesmo disco físico, não há qualquer relação entre os contentores que não o que é definido pelo serviço de diretório, que por sua vez é ditado pelo administrador do arrendatário.

### <a name="azure-role-based-access-control-azure-rbac"></a>Controlo de acesso baseado em funções do Azure (RBAC do Azure)

[O controlo de acesso baseado em funções (Azure RBAC) ajuda-o](../../role-based-access-control/overview.md) a partilhar vários componentes disponíveis dentro de uma subscrição do Azure, fornecendo uma gestão de acesso de grãos finos para o Azure. O Azure RBAC permite-lhe segregar funções dentro da sua organização e conceder acesso com base no que os utilizadores precisam para desempenhar os seus trabalhos. Em vez de dar a todos permissões ilimitadas na subscrição ou recursos do Azure, você pode permitir apenas certas ações.

O Azure RBAC tem três funções básicas que se aplicam a todos os tipos de recursos:

- **O proprietário** tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outros.

- **O contribuinte** pode criar e gerir todos os tipos de recursos Azure, mas não pode conceder acesso a outros.

- **O leitor** pode ver os recursos Azure existentes.

![Controlo de acesso baseado em funções do Azure (RBAC do Azure)](./media/isolation-choices/azure-isolation-fig3.png)

O resto das funções do Azure no Azure permitem a gestão de recursos específicos do Azure. Por exemplo, a função Contribuidor de Máquina Virtual permite ao utilizador criar e gerir máquinas virtuais. Não lhes dá acesso à Rede Virtual Azure ou à sub-rede a que a máquina virtual se conecta.

[As funções incorporadas da RBAC](../../role-based-access-control/built-in-roles.md) listam as funções disponíveis no Azure. Especifica as operações e o âmbito que cada papel incorporado concede aos utilizadores. Se procura definir os seus próprios papéis para ainda mais controlo, veja como construir [papéis personalizados no Azure RBAC.](../../role-based-access-control/custom-roles.md)

Algumas outras capacidades para o Azure Ative Directory incluem:

- O Azure AD permite SSO para aplicações SaaS, independentemente de onde estão alojadas. Algumas aplicações estão federadas com o Azure AD e outras utilizam SSO com palavra-passe. As aplicações federadas também podem suportar o fornecimento de utilizadores e [a abóbada de senha](https://www.techopedia.com/definition/31415/password-vault).

- O acesso aos dados do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado através da autenticação. Cada conta de armazenamento tem uma chave primária[(chave de conta de armazenamento,](../../storage/common/storage-account-create.md)ou SAK) e uma chave secreta secundária (a assinatura de acesso partilhado, ou SAS).

- A Azure AD fornece identidade como serviço através da federação através da utilização [de Serviços da Federação de Diretórios Ativos,](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)sincronização e replicação com diretórios no local.

- [A Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) é o serviço de autenticação multi-factor que exige que os utilizadores verifiquem as inscrições utilizando uma aplicação móvel, chamada telefónica ou mensagem de texto. Pode ser usado com Azure AD para ajudar a garantir recursos no local com o servidor Azure Multi-Factor Authentication, e também com aplicações e diretórios personalizados usando o SDK.

- [Os Serviços de Domínio Azure AD](https://azure.microsoft.com/services/active-directory-ds/) permitem-lhe juntar máquinas virtuais Azure a um domínio ative Directory sem implantar controladores de domínio. Pode iniciar sôs-se nestas máquinas virtuais com as suas credenciais de Ative Directory corporativo e administrar máquinas virtuais unidas a domínios, utilizando a Política de Grupo para impor linhas de segurança em todas as suas máquinas virtuais Azure.

- [O Azure Ative Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) oferece um serviço de gestão de identidade global altamente disponível para aplicações viradas para o consumidor que escala para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os seus consumidores podem iniciar scontabilidade de todas as suas aplicações através de experiências personalizáveis, utilizando as suas contas sociais existentes ou criando credenciais.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolamento dos administradores da Microsoft & eliminação de dados

A Microsoft toma medidas fortes para proteger os seus dados de acesso ou utilização inadequados por pessoas não autorizadas. Estes processos e controlos operacionais são apoiados pelos [Termos dos Serviços Online,](https://aka.ms/Online-Services-Terms)que oferecem compromissos contratuais que regem o acesso aos seus dados.

- Os engenheiros da Microsoft não têm acesso predefinido aos seus dados na nuvem. Em vez disso, é-lhes concedido acesso, sob supervisão de gestão, apenas quando necessário. Esse acesso é cuidadosamente controlado e registado, e revogado quando já não é necessário.
- A Microsoft pode contratar outras empresas para fornecer serviços limitados em seu nome. Os subcontratantes podem aceder aos dados dos clientes apenas para prestar os serviços para os quais, contratámo-los para fornecer, e estão proibidos de o utilizar para qualquer outro fim. Além disso, estão contratualmente obrigados a manter a confidencialidade da informação dos nossos clientes.

Os serviços empresariais com certificações auditadas como a ISO/IEC 27001 são regularmente verificados pela Microsoft e pelas empresas de auditoria acreditadas, que realizam auditorias de amostra para atestar esse acesso, apenas para fins comerciais legítimos. Pode sempre aceder aos dados do seu próprio cliente a qualquer momento e por qualquer motivo.

Se eliminar quaisquer dados, o Microsoft Azure elimina os dados, incluindo quaisquer cópias em cache ou cópias de backup. No caso dos serviços em curso, essa supressão ocorrerá no prazo de 90 dias após o termo do período de retenção. (Os serviços in-âmbito são definidos na secção De Tratamento de Dados dos [nossos Termos de Serviços Online](https://aka.ms/Online-Services-Terms).)

Se uma unidade de disco utilizada para armazenamento sofrer uma falha de hardware, esta é apagada [ou destruída](https://microsoft.com/trustcenter/privacy/you-own-your-data) de forma segura antes que a Microsoft a devolva ao fabricante para substituição ou reparação. Os dados sobre a unidade são substituídos para garantir que os dados não podem ser recuperados por qualquer meio.

## <a name="compute-isolation"></a>Isolamento computacional

O Microsoft Azure fornece vários serviços de computação baseados na nuvem que incluem uma ampla seleção de instâncias de computação & serviços que podem escalar para cima e para baixo automaticamente para atender às necessidades da sua aplicação ou empresa. Estas instâncias de computação e serviço oferecem isolamento a vários níveis para garantir dados sem sacrificar a flexibilidade na configuração que os clientes exigem.

### <a name="isolated-virtual-machine-sizes"></a>Tamanhos de máquina virtual isolados

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Anfitriões dedicados

Além dos anfitriões isolados descritos na secção anterior, a Azure também oferece anfitriões dedicados. Os anfitriões dedicados em Azure é um serviço que fornece servidores físicos que podem hospedar uma ou mais máquinas virtuais, e que são dedicados a uma única subscrição do Azure. Os anfitriões dedicados proporcionam isolamento de hardware ao nível do servidor físico. Nenhum outro VMs será colocado nos seus anfitriões. Os anfitriões dedicados são implantados nos mesmos centros de dados e partilham a mesma rede e infraestrutura de armazenamento subjacente que outros anfitriões não isolados. Para mais informações, consulte a visão geral detalhada dos [anfitriões dedicados a Azure.](../../virtual-machines/dedicated-hosts.md)

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hiper-V & isolamento do OS raiz entre VM raiz vm & VMs convidados

A plataforma de computação do Azure baseia-se na virtualização da máquina, o que significa que todo o código do cliente é executado numa máquina virtual Hyper-V. Em cada nó Azure (ou ponto final da rede), existe um Hipervisor que passa diretamente sobre o hardware e divide um nó em um número variável de Máquinas Virtuais convidadas (VMs).

![Hiper-V & isolamento do OS raiz entre VM raiz vm & VMs convidados](./media/isolation-choices/azure-isolation-fig4.jpg)

Cada nó também tem um VM raiz especial, que executa o Host OS. Um limite crítico é o isolamento da raiz VM dos VMs convidados e os VMs convidados uns dos outros, geridos pelo hipervisor e o socir raiz. O emparelhamento de sistemas hipervisor/raiz do SISTEMA aproveita as décadas de experiência de segurança do sistema operativo da Microsoft e a aprendizagem mais recente do Hyper-V da Microsoft, para proporcionar um forte isolamento dos VMs convidados.

A plataforma Azure utiliza um ambiente virtualizado. As instâncias do utilizador funcionam como máquinas virtuais autónomas que não têm acesso a um servidor de anfitrião físico.

O hipervisor Azure age como um micro-núcleo e passa todos os pedidos de acesso de hardware de máquinas virtuais de hóspedes para o anfitrião para processamento usando uma interface de memória partilhada chamada VMBus. Isto impede os utilizadores de obterem acesso de leitura/escrita/execução não processado ao sistema e atenua o risco de partilha de recursos do sistema.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Algoritmo avançado de colocação de VM & proteção contra ataques de canais laterais

Qualquer ataque cross-VM envolve dois passos: colocar um VM controlado pelo adversário no mesmo hospedeiro que um dos VM da vítima, e, em seguida, quebrar o limite de isolamento para roubar informações confidenciais da vítima ou afetar o seu desempenho por ganância ou vandalismo. O Microsoft Azure fornece proteção em ambas as etapas usando um algoritmo avançado de colocação de VM e proteção contra todos os ataques de canais laterais conhecidos, incluindo VMs de vizinhos barulhentos.

### <a name="the-azure-fabric-controller"></a>O Controlador de Tecidos Azure

O Controlador de Tecidos Azure é responsável pela alocação de recursos de infraestrutura às cargas de trabalho dos inquilinos, e gere comunicações unidirecionais desde o hospedeiro a máquinas virtuais. O algoritmo de colocação de VM do controlador de tecido Azure é altamente sofisticado e quase impossível de prever como nível de hospedeiro físico.

![O Controlador de Tecidos Azure](./media/isolation-choices/azure-isolation-fig5.png)

O hipervisor Azure impõe a separação da memória e do processo entre máquinas virtuais, e liga de forma segura o tráfego de rede aos inquilinos convidados do SO. Isto elimina a possibilidade de ataque de canal lateral a nível de VM.

Em Azure, a raiz VM é especial: executa um sistema operativo endurecido chamado o SISTEMA raiz que acolhe um agente de tecido (FA). As FAs são usadas por sua vez para gerir agentes convidados (GA) dentro dos sistemas operativos dos hóspedes em VMs do cliente. As FAs também gerem os nós de armazenamento.

A coleção de hipervisor Azure, raiz OS/FA e VMs/GAs do cliente compreende um nó computacional. As FAs são geridas por um controlador de tecido (FC), que existe fora dos nosmos de computação e armazenamento (os clusters de computação e armazenamento são geridos por FCs separados). Se um cliente atualizar o ficheiro de configuração da sua aplicação enquanto está em execução, o FC comunica com a FA, que então contacta GAs, que notificam a aplicação da alteração de configuração. Em caso de falha de hardware, o FC irá automaticamente encontrar hardware disponível e reiniciar o VM lá.

![Controlador de tecido Azure](./media/isolation-choices/azure-isolation-fig6.jpg)

A comunicação de um controlador de tecido a um agente é unidirecional. O agente implementa um serviço protegido por SSL que apenas responde aos pedidos do controlador. Não pode iniciar ligações ao controlador ou a outros nós internos privilegiados. O FC trata todas as respostas como se não fossem falsas.

![Controlador de tecidos](./media/isolation-choices/azure-isolation-fig7.png)

O isolamento estende-se a partir do Root VM dos VMs convidados, e os VMs convidados uns dos outros. Os nós computacional também estão isolados dos nós de armazenamento para uma maior proteção.

O hipervisor e o os hospedeiro fornecem pacotes de rede - filtros para ajudar a garantir que máquinas virtuais não fidedinhas não podem gerar tráfego falsificado ou receber tráfego não endereçado a eles, tráfego direto para pontos finais de infraestrutura protegidos, ou enviar/receber tráfego de transmissão inadequado.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Regras adicionais configuradas pelo agente controlador de tecido para isolar vm

Por predefinição, todo o tráfego é bloqueado quando uma máquina virtual é criada e, em seguida, o agente controlador de tecido configura o filtro do pacote para adicionar regras e exceções para permitir o tráfego autorizado.

Existem duas categorias de regras que são programadas:

- **Configuração de máquinas ou regras de infraestrutura:** Por predefinição, toda a comunicação está bloqueada. Existem exceções para permitir que uma máquina virtual envie e receba tráfego DHCP e DNS. As máquinas virtuais também podem enviar tráfego para a internet "pública" e enviar tráfego para outras máquinas virtuais dentro da mesma Rede Virtual Azure e do servidor de ativação do SO. A lista de destinos de saída permitidos por máquinas virtuais não inclui sub-redes de router Azure, gestão Azure e outras propriedades da Microsoft.
- **Ficheiro de configuração de função:** Isto define as Listas de Controlo de Acesso de Entrada (ACLs) com base no modelo de serviço do arrendatário.

### <a name="vlan-isolation"></a>Isolamento VLAN

Há três VLANs em cada cluster:

![Isolamento VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)

- O VLAN principal - interliga os acenos de clientes não fidedidos
- O FC VLAN – contém FCs confiáveis e sistemas de suporte
- O dispositivo VLAN – contém rede fidedigna e outros dispositivos de infraestrutura

A comunicação é permitida do FC VLAN para o VLAN principal, mas não pode ser iniciada do VLAN principal para o FC VLAN. A comunicação também é bloqueada do VLAN principal para o dispositivo VLAN. Isto garante que mesmo que um nó que executa o código do cliente esteja comprometido, não pode atacar nós no FC ou no dispositivo VLANs.

## <a name="storage-isolation"></a>Isolamento de Armazenamento

### <a name="logical-isolation-between-compute-and-storage"></a>Isolamento lógico entre computação e armazenamento

Como parte do seu design fundamental, o Microsoft Azure separa a computação baseada em VM do armazenamento. Esta separação permite que a computação e armazenamento se dimensionem de forma independente, facilitando a disponibilização de vários arrendamentos e isolamento.

Portanto, o Azure Storage funciona em hardware separado sem conectividade de rede com a Azure Compute exceto logicamente. Isto significa que quando um disco virtual é criado, o espaço do disco não é atribuído para toda a sua capacidade. Em vez disso, é criada uma tabela que mapeia endereços no disco virtual para áreas no disco físico e essa tabela está inicialmente vazia. **A primeira vez que um cliente escreve dados no disco virtual, o espaço no disco físico é atribuído e um ponteiro para ele é colocado na tabela.**

### <a name="isolation-using-storage-access-control"></a>Isolamento usando o controlo de acesso ao armazenamento

**O Access Control in Azure Storage** tem um modelo simples de controlo de acesso. Cada subscrição do Azure pode criar uma ou mais Contas de Armazenamento. Cada Conta de Armazenamento tem uma única chave secreta que é usada para controlar o acesso a todos os dados dessa Conta de Armazenamento.

![Isolamento usando o controlo de acesso ao armazenamento](./media/isolation-choices/azure-isolation-fig9.png)

**O acesso aos dados de armazenamento do Azure (incluindo tabelas)** pode ser controlado através de um token [SAS (Shared Access Signature),](../../storage/common/storage-sas-overview.md) que concede acesso alargado. O SAS é criado através de um modelo de consulta (URL), assinado com o [SAK (Chave de Conta de Armazenamento)](/previous-versions/azure/reference/ee460785(v=azure.100)). Esse [URL assinado](../../storage/common/storage-sas-overview.md) pode ser dado a outro processo (isto é, delegado), que pode então preencher os detalhes da consulta e fazer o pedido do serviço de armazenamento. Um SAS permite-lhe conceder acesso baseado no tempo aos clientes sem revelar a chave secreta da conta de armazenamento.

O SAS significa que podemos conceder a um cliente permissões limitadas, a objetos na nossa conta de armazenamento por um determinado período de tempo e com um conjunto especificado de permissões. Podemos conceder estas permissões limitadas sem ter de partilhar as chaves de acesso à sua conta.

### <a name="ip-level-storage-isolation"></a>Isolamento de armazenamento de nível IP

Pode estabelecer firewalls e definir um intervalo de endereços IP para os seus clientes de confiança. Com uma gama de endereços IP, apenas os clientes que tenham um endereço IP dentro do intervalo definido podem ligar-se ao [Azure Storage](../../storage/blobs/security-recommendations.md).

Os dados de armazenamento IP podem ser protegidos de utilizadores não autorizados através de um mecanismo de rede que é usado para alocar um túnel de tráfego dedicado ou dedicado ao armazenamento IP.

### <a name="encryption"></a>Encriptação

A Azure oferece os seguintes tipos de Encriptação para proteger os dados:

- Encriptação de dados em circulação
- Encriptação de dados inativos

#### <a name="encryption-in-transit"></a>Encriptação em Trânsito

A encriptação em trânsito é um mecanismo de proteção dos dados quando são transmitidos através de redes. Com o Azure Storage, pode proteger os dados utilizando:

- [Encriptação ao nível do transporte](../../storage/blobs/security-recommendations.md), como HTTPS quando transfere dados para dentro ou para fora do Azure Storage.
- [Encriptação de](../../storage/blobs/security-recommendations.md)fios , como encriptação SMB 3.0 para ações do Ficheiro Azure.
- [Encriptação do lado do cliente](../../storage/blobs/security-recommendations.md), para encriptar os dados antes de serem transferidos para o armazenamento e para desencriptar os dados após a sua transferência para fora do armazenamento.

#### <a name="encryption-at-rest"></a>Encriptação em Repouso

Para muitas organizações, [a encriptação de dados em repouso](isolation-choices.md) é um passo obrigatório para a privacidade dos dados, conformidade e soberania de dados. Existem três funcionalidades do Azure que fornecem encriptação de dados que estão "em repouso":

- [A Encriptação do Serviço de Armazenamento](../../storage/blobs/security-recommendations.md) permite-lhe solicitar que o serviço de armazenamento criptografe automaticamente os dados ao escrevê-lo para o Azure Storage.
- [A encriptação do lado do cliente](../../storage/blobs/security-recommendations.md) também fornece a funcionalidade de encriptação em repouso.
- [A encriptação do disco Azure](./azure-disk-encryption-vms-vmss.md) permite encriptar os discos de SISTEMA e discos de dados utilizados por uma máquina virtual IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[A encriptação do disco Azure](./azure-disk-encryption-vms-vmss.md) para máquinas virtuais (VMs) ajuda-o a abordar os requisitos de segurança organizacional e de conformidade encriptando os seus discos VM (incluindo discos de arranque e dados) com chaves e políticas que controla no [Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/).

A solução de encriptação de disco para Windows baseia-se na [encriptação de unidade do Microsoft BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)), e a solução Linux [baseia-se na criptomoeda dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

A solução suporta os seguintes cenários para IaaS VMs quando estão ativados no Microsoft Azure:

- Integração com cofre de chaves Azure
- VMs de nível padrão: A, D, DS, G, GS, e assim por diante, série IaaS VMs
- Habilitação de encriptação em Windows e Linux IaaS VMs
- Desativar encriptação em SISTEMA e unidades de dados para O Windows IaaS VMs
- Desativar encriptação em unidades de dados para Linux IaaS VMs
- Habilitação de encriptação em IaaS VMs que estão executando o cliente Windows OS
- Habilitando encriptação em volumes com caminhos de montagem
- Habilitando a encriptação em VMs Linux que são configurados com tiragem de disco (RAID) usando [mdadm](https://en.wikipedia.org/wiki/Mdadm)
- Habilitação da encriptação nos VMs do Linux utilizando [o LVM (Logical Volume Manager)](/windows/win32/fileio/about-volume-management) para discos de dados
- Habilitando a encriptação em VMs do Windows que são configurados usando espaços de armazenamento
- Todas as regiões públicas do Azure são apoiadas

A solução não suporta os seguintes cenários, funcionalidades e tecnologia no lançamento:

- Nível básico IaaS VMs
- Encriptação incapacitante numa unidade de SO para Linux IaaS VMs
- IaaS VMs que são criados usando o método clássico de criação de VM
- Integração com o seu Serviço de Gestão chave no local
- Ficheiros Azure (sistema de ficheiros partilhados), Sistema de Ficheiros de Rede (NFS), volumes dinâmicos e VMs do Windows configurados com sistemas RAID baseados em software

## <a name="sql-database-isolation"></a>Isolamento da base de dados SQL

A Base de Dados SQL é um serviço de bases de dados relacionais na cloud da Microsoft baseado no motor Microsoft SQL Server líder de mercado e com capacidade para processar cargas de trabalho críticas para missões. A SQL Database oferece um isolamento previsível de dados ao nível da conta, geografia/região baseada em networking, tudo com administração quase nula.

### <a name="sql-database-application-model"></a>Modelo de aplicação de base de dados SQL

[O Microsoft SQL Database](../../azure-sql/database/single-database-create-quickstart.md) é um serviço de base de dados relacional baseado na nuvem construído em tecnologias do SQL Server. Fornece um serviço de base de dados altamente disponível, escalável e multi-inquilino hospedado pela Microsoft na nuvem.

Do ponto de vista da aplicação, a Base de Dados SQL fornece a seguinte hierarquia: Cada nível tem uma a muita contenção de níveis abaixo.

![Modelo de aplicação de base de dados SQL](./media/isolation-choices/azure-isolation-fig10.png)

A conta e subscrição são conceitos da plataforma Microsoft Azure para associar faturação e gestão.

Os servidores e bases de dados lógicos do SQL são conceitos específicos da Base de Dados SQL e são geridos através da utilização da Base de Dados SQL, das interfaces OData e TSQL fornecidas ou através do portal Azure.

Os servidores na Base de Dados SQL não são casos físicos ou VM, em vez disso são coleções de bases de dados, partilha de políticas de gestão e segurança, que são armazenadas na chamada base de dados "lógico mestre".

![Base de Dados SQL](./media/isolation-choices/azure-isolation-fig11.png)

As bases de dados lógicos incluem:

- Logins SQL utilizados para ligar ao servidor
- Regras da firewall

A faturação e as informações relacionadas com a utilização das bases de dados a partir do mesmo servidor não estão garantidas na mesma instância física do cluster, em vez disso, as aplicações devem fornecer o nome da base de dados-alvo ao ligar.

Do ponto de vista do cliente, um servidor é criado numa região geográfica enquanto a criação real do servidor acontece num dos clusters da região.

### <a name="isolation-through-network-topology"></a>Isolamento através da Topologia da Rede

Quando um servidor é criado e o seu nome DNS é registado, o nome DNS aponta para o chamado endereço "Gateway VIP" no centro de dados específico onde o servidor foi colocado.

Atrás do VIP (endereço IP virtual), temos uma coleção de serviços de gateway apátrida. Em geral, os gateways envolvem-se quando é necessária coordenação entre várias fontes de dados (base de dados principal, base de dados de utilizadores, etc.). Os serviços gateway implementam os seguintes:

- **Procuração de ligação TDS.** Isto inclui localizar a base de dados do utilizador no cluster backend, implementar a sequência de login e, em seguida, encaminhar os pacotes TDS para o backend e para trás.
- **Gestão de bases de dados.** Isto inclui a implementação de uma coleção de fluxos de trabalho para fazer operações de base de dados CREATE/ALTER/DROP. As operações de base de dados podem ser invocadas através do cheiro de pacotes TDS ou de APIs OData explícitos.
- OPERAções de login/utilizador CREATE/ALTER/DROP
- Operações de gestão de servidores via OData API

![Isolamento através da Topologia da Rede](./media/isolation-choices/azure-isolation-fig12.png)

O nível por trás dos portões chama-se "back-end". É aqui que todos os dados são armazenados de forma altamente disponível. Cada pedaço de dados é dito pertencer a uma "divisão" ou "unidade de failover", cada um deles com pelo menos três réplicas. As réplicas são armazenadas e replicadas pelo motor SQL Server e geridas por um sistema de failover muitas vezes referido como "tecido".

Geralmente, o sistema back-end não comunica a saída para outros sistemas como precaução de segurança. Isto é reservado aos sistemas no nível frontal (gateway). As máquinas de nível de porta de entrada têm privilégios limitados nas máquinas traseiras para minimizar a superfície de ataque como um mecanismo de defesa em profundidade.

### <a name="isolation-by-machine-function-and-access"></a>Isolamento pela Função e Acesso da Máquina

Base de Dados SQL (é composta por serviços em execução em diferentes funções de máquina. A BASE de Dados SQL é dividida em ambientes "backend" cloud database e ambientes "front-end" (Gateway/Management), com o princípio geral do tráfego apenas entrando no back-end e não fora. O ambiente frontal pode comunicar com o mundo exterior de outros serviços e, em geral, tem apenas permissões limitadas no back-end (o suficiente para chamar os pontos de entrada que precisa de invocar).

## <a name="networking-isolation"></a>Isolamento em rede

A implantação de azul tem várias camadas de isolamento de rede. O diagrama que se segue mostra várias camadas de isolamento de rede que o Azure fornece aos clientes. Estas camadas são nativas na própria plataforma Azure e características definidas pelo cliente. Vindo da Internet, Azure DDoS proporciona isolamento contra ataques em larga escala contra Azure. A próxima camada de isolamento são os endereços IP públicos definidos pelo cliente (pontos finais), que são usados para determinar que tráfego pode passar através do serviço de nuvem para a rede virtual. O isolamento da rede virtual Native Azure garante o isolamento total de todas as outras redes, e que o tráfego flui apenas através de caminhos e métodos configurados pelo utilizador. Estes caminhos e métodos são a próxima camada, onde os NSGs, UDR e aparelhos virtuais de rede podem ser usados para criar limites de isolamento para proteger as implementações de aplicações na rede protegida.

![Isolamento em rede](./media/isolation-choices/azure-isolation-fig13.png)

**Isolamento do tráfego:** Uma [rede virtual](../../virtual-network/virtual-networks-overview.md) é o limite de isolamento de tráfego na plataforma Azure. As máquinas virtuais (VMs) numa rede virtual não podem comunicar diretamente com VMs numa rede virtual diferente, mesmo que ambas as redes virtuais sejam criadas pelo mesmo cliente. O isolamento é uma propriedade crítica que garante que os VMs do cliente e a comunicação permanecem privados dentro de uma rede virtual.

[A sub-rede](../../virtual-network/virtual-networks-overview.md) oferece uma camada adicional de isolamento com rede virtual baseada na gama IP. Endereços IP na rede virtual, pode dividir uma rede virtual em várias subesí redes para organização e segurança. As VMs e as instâncias de função de PaaS implementadas em sub-redes (nas mesmas ou em diferentes) dentro de uma VNet podem comunicar entre si sem qualquer configuração adicional. Também pode configurar [o grupo de segurança da rede (NSGs)](../../virtual-network/virtual-networks-overview.md) para permitir ou negar o tráfego de rede a uma instância VM baseada em regras configuradas na lista de controlo de acessos (ACL) de NSG. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [opções de isolamento de rede para máquinas em redes virtuais Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Isto inclui o cenário clássico frontal e traseiro em que as máquinas de uma determinada rede ou sub-rede de back-end só podem permitir que determinados clientes ou outros computadores se conectem a um determinado ponto final com base numa lista de endereços IP.

- Saiba mais sobre [o isolamento virtual da máquina em Azure.](../../virtual-machines/isolation.md) O Azure Compute oferece tamanhos de máquinas virtuais que são isolados a um tipo de hardware específico e dedicados a um único cliente.