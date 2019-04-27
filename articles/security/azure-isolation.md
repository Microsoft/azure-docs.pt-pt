---
title: Isolamento na Cloud pública do Azure | Documentos da Microsoft
description: Saiba mais sobre serviços informáticos com base na cloud que incluem uma ampla seleção de instâncias de computação e serviços que podem aumenta e reduz automaticamente para satisfazer as necessidades do seu aplicativo ou a empresa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f5d1c66cb049ab9ec52db619d55a4bb3e485e4b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588537"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolamento na Cloud pública do Azure
##  <a name="introduction"></a>Introdução
### <a name="overview"></a>Descrição geral
Para ajudá-lo do Azure atual e potencial clientes compreenderem e usar os recursos relacionados à segurança vários disponíveis no e referentes à plataforma do Azure, a Microsoft desenvolveu uma série de White Papers, visões gerais de segurança, práticas recomendadas, e Listas de verificação.
Os tópicos vão em termos de amplitude e profundidade e são atualizados periodicamente. Este documento é parte dessa série, conforme resumido na seguinte secção Abstrata.

### <a name="azure-platform"></a>Plataforma do Azure
O Azure é uma plataforma de serviço de cloud aberta e flexível que suporta uma seleção ampla de sistemas operativos, linguagens de programação, arquiteturas, ferramentas, bases de dados e dispositivos. Pode, por exemplo:
- Execute contentores do Linux com a integração de Docker;
- Crie aplicações com JavaScript, Python, .NET, PHP, Java e node. js; e
- Crie back-ends para dispositivos iOS, Android e Windows dispositivos.

Microsoft Azure suporta as mesmas tecnologias milhões de desenvolvedores e profissionais de TI confiam e confiam.

Quando criar ou migrar ativos de TI para, um fornecedor de serviços de cloud pública, está a depender das capacidades dessa organização para proteger as suas aplicações e dados com os serviços e os controles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. Além disso, o Azure disponibiliza-lhe um vasto leque de opções de segurança configuráveis e a capacidade para controlá-las de modo a que possa personalizar a segurança e satisfazer os requisitos únicos das suas implementações. Este documento ajuda-o a cumprir esses requisitos.

### <a name="abstract"></a>Abstrato

Microsoft Azure permite-lhe executar aplicações e máquinas virtuais (VMs) em infraestrutura física compartilhada. Uma das motivações principais económicas para executar aplicações num ambiente de nuvem é a capacidade de distribuir o custo de recursos compartilhados entre vários clientes. Esta prática de vários inquilinos melhora a eficiência por multiplexação de recursos entre os clientes diferentes a custos baixos. Infelizmente, ele também introduz o risco de compartilhamento de servidores físicos e outros recursos de infraestrutura para executar as aplicações confidenciais e VMs que poderão pertencer a um usuário arbitrário e potencialmente malicioso.

Este artigo descreve como o Microsoft Azure fornece isolamento contra utilizadores maliciosos e não maliciosos e serve como um guia para arquitetar soluções na cloud ao proporcionar várias opções de isolamento para arquitetos. Este white paper concentra-se na tecnologia da plataforma do Azure e controlos de segurança do lado do cliente e não tenta SLAs de endereço, considerações de prática de DevOps e modelos de preços.

## <a name="tenant-level-isolation"></a>Isolamento em nível de inquilino
Uma das principais vantagens da computação em nuvem é o conceito de uma infraestrutura partilhada, comuns em grande número de clientes em simultâneo, que leva à economia de escala. Esse conceito é chamado de vários inquilinos. A Microsoft trabalha continuamente para se certificar de que a arquitetura de multi-inquilino do Microsoft Cloud Azure suporta segurança, da confidencialidade, privacidade, integridade e padrões de disponibilidade.

Na área de trabalho ativada na nuvem, um inquilino pode ser definido com um cliente ou uma organização que possui e gere uma instância específica do referido serviço em nuvem. Com a plataforma de identidade fornecida pelo Microsoft Azure, um inquilino é simplesmente uma instância dedicada do Azure Active Directory (Azure AD) que sua organização recebe e detém quando se inscreve num serviço de nuvem da Microsoft.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Tal como um edifício de escritórios empresariais é um recurso seguro específico apenas para a sua organização, um diretório do Azure AD também foi concebido para ser um recurso com utilização segura apenas para a sua organização. A arquitetura do Azure AD impede que os dados do cliente e as informações de identidade se misturem. Isto significa que os utilizadores e administradores de um diretório do Azure AD não podem, acidental ou intencionalmente, aceder aos dados de outro diretório.

### <a name="azure-tenancy"></a>Inquilinos do Azure
Inquilinos do Azure (subscrição do Azure) refere-se para uma relação de "cliente/billing" e um exclusivo [inquilino](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) na [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Isolamento em nível de inquilino no Microsoft Azure é obtido com o Azure Active Directory e [controles baseados em função](https://docs.microsoft.com/azure/role-based-access-control/overview) oferecidos por ela. Cada subscrição do Azure está associada um diretório do Azure Active Directory (AD).

Os utilizadores, grupos e aplicações a partir desse diretório podem gerir os recursos na subscrição do Azure. Pode atribuir estes direitos de acesso com o portal do Azure, ferramentas de linha de comandos do Azure e APIs de gestão do Azure. Um inquilino do Azure AD está isolado logicamente com limites de segurança, para que nenhum cliente possa acessar ou comprometer inquilinos conjunta, maliciosa ou acidentalmente. O Azure AD é executado em servidores de "bare-metal" isolados num segmento de rede segregadas, onde a filtragem de pacotes ao nível do anfitrião e o Windows Firewall bloqueiam ligações indesejadas e tráfego.

- Acesso a dados no Azure AD requer autenticação do utilizador através de um serviço de token de segurança (STS). Informações sobre a existência do utilizador, estado ativado e função são utilizadas pelo sistema de autorização para determinar se o acesso solicitado para o inquilino de destino está autorizado para este utilizador nesta sessão.

![Inquilinos do Azure](./media/azure-isolation/azure-isolation-fig1.png)


- Os inquilinos são contentores discretos e não existe nenhuma relação entre estes.

- Sem acesso entre inquilinos, a menos que o administrador de inquilinos concede-lo por meio de Federação ou o aprovisionamento de contas de utilizador de outros inquilinos.

- Acesso físico aos servidores que compõem o serviço do Azure AD e o acesso direto aos sistemas de back-end do Azure AD, são restritos.

- Utilizadores do Azure AD não têm acesso a ativos físicos ou localizações e, portanto, não é possível ignorar as lógicas RBAC verificações de política indicadas a seguir.

Para diagnóstico e necessidades de manutenção, um modelo operacional que emprega um sistema de elevação de privilégios just-in-time é necessário e utilizado. O Azure AD Privileged Identity Management (PIM) introduz o conceito de administrador elegível. [Administradores elegíveis](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) deve ser a utilizadores que necessitam de privilégios de acesso ocasional, mas não todos os dias. A função está inativa até que o utilizador precise de acesso. Nessa altura, o utilizador realiza um processo de ativação e torna-se num administrador ativo durante uma quantidade pré-determinada de tempo.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

O Azure Active Directory aloja cada inquilino no seu próprio contentor protegido, com as políticas e permissões para e dentro do contentor unicamente detida e gerida pelo inquilino.

O conceito de recipientes de inquilino está profundamente incluído no serviço de diretório em todas as camadas, de portais para armazenamento persistente.

Mesmo quando os metadados de vários inquilinos do Azure Active Directory são armazenados no mesmo disco físico, existe nenhuma relação entre os contentores que não seja o que é definido pelo serviço de diretório, por sua vez, é ditado pelo administrador de inquilinos.

### <a name="azure-role-based-access-control-rbac"></a>Controlo de acesso baseado em função do Azure (RBAC)
[Do Azure com base em função de controlo de acesso (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ajuda-o a partilhar os vários componentes disponíveis dentro de uma subscrição do Azure ao fornecer gestão de acessos detalhada para o Azure. RBAC do Azure permite-lhe segregar funções na sua organização e conceder acesso com base na qual os utilizadores precisam desempenhar as suas funções. Em vez de dar todas as pessoas sem restrições permissões na subscrição do Azure ou recursos, pode permitir que apenas determinadas ações.

RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recursos:

- **Proprietário** tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outras pessoas.

- **Contribuinte** pode criar e gerir todos os tipos de recursos do Azure, mas não é possível conceder acesso a outras pessoas.

- **Leitor** pode ver os recursos do Azure existentes.

![Controlo de acesso baseado em função do Azure](./media/azure-isolation/azure-isolation-fig3.png)

O restante das funções do RBAC do Azure permitem a gestão de recursos específicos do Azure. Por exemplo, a função de contribuinte de Máquina Virtual permite ao utilizador criar e gerir máquinas virtuais. Ele não dá-lhes acesso a rede Virtual do Azure ou a sub-rede que a máquina virtual se liga a.

[Funções incorporadas do RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) uma lista das funções disponíveis no Azure. Especifica as operações e o âmbito em que cada função incorporada concede a utilizadores. Se estiver procurando para definir suas próprias funções para o controle ainda mais, veja como criar [funções personalizadas no Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Outras funcionalidades do Azure Active Directory incluem:
- O Azure AD permite SSO para aplicações SaaS, independentemente de onde estão alojadas. Algumas aplicações estão federadas com o Azure AD e outras utilizam SSO com palavra-passe. As aplicações federadas também podem suportar o aprovisionamento de utilizador e [cofres de palavras de palavra-passe](https://www.techopedia.com/definition/31415/password-vault).

- O acesso aos dados do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado através da autenticação. Cada conta de armazenamento tem uma chave primária ([chave de conta de armazenamento](https://docs.microsoft.com/azure/storage/storage-create-storage-account), ou SAK) e uma chave secreta secundária (a assinatura de acesso partilhado ou SAS).

- O Azure AD fornece identidade como um serviço através de Federação utilizando [serviços de Federação do Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), sincronização e replicação de diretórios no local.

- [O Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) é o serviço de autenticação multifator que requer que os utilizadores verificar inícios de sessão com uma aplicação móvel, chamada telefónica ou mensagem de texto. Ele pode ser usado com o Azure AD para o ajudar a proteger recursos no local com o servidor multi-factor Authentication do Azure e também com aplicativos personalizados e diretórios com o SDK.

- [O Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite-lhe associar máquinas virtuais do Azure a um domínio do Active Directory sem ter de implementar controladores de domínio. Pode iniciar sessão nestas máquinas virtuais com as suas credenciais empresariais do Active Directory e administrar as máquinas de virtuais associados a um domínio utilizando a política de grupo para impor linhas de base de segurança em todas as máquinas virtuais do Azure.

- [O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) fornece um serviço de gestão de identidade global altamente disponível para aplicações voltadas para consumidores que se dimensionam até centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão todas as suas aplicações através de experiências personalizáveis através das respetivas contas de redes sociais existentes ou criando credenciais.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolamento de administradores do Microsoft e a eliminação de dados
A Microsoft leva medidas fortes para proteger os dados de acesso inadequado ou utilizar por pessoas não autorizadas. Esses processos e controlos operacionais são apoiados pela [termos dos Online Services](https://aka.ms/Online-Services-Terms), que oferecem os compromissos contratuais que regem o acesso aos seus dados.

-   Engenheiros da Microsoft não tem predefinição acesso aos seus dados na cloud. Em vez disso, é-lhes concedida acesso, em supervisão de gerenciamento, apenas quando necessário. Esse acesso é cuidadosamente controlado com sessão iniciado e revogado quando já não for necessário.

-   A Microsoft pode contratar outras empresas para fornecer serviços limitados em seu nome. Os subcontratantes podem aceder aos dados do cliente apenas para fornecer que os serviços para os quais, tenhamos contratado para fornecer e eles são proibidos de usá-lo para qualquer outra finalidade. Além disso, eles são contratualmente ligados para manter a confidencialidade das informações dos clientes.

Serviços de negócios com certificações auditadas, tais como a ISO/IEC 27001 regularmente são verificados por firmas de auditoria credenciadas, que efetuar auditorias de amostra para atestar que o acesso, apenas para fins comerciais legítimos e Microsoft. Sempre pode acessar seus próprios dados de cliente a qualquer momento e por qualquer motivo.

Se eliminar quaisquer dados, o Microsoft Azure elimina os dados, incluindo quaisquer cópias em cache ou cópia de segurança. Para os serviços de dentro do âmbito, que a eliminação irá ocorrer dentro de 90 dias após o fim do período de retenção. (Serviços de dentro do âmbito são definidos na secção de termos de processamento de dados do nosso [termos dos Online Services](https://aka.ms/Online-Services-Terms).)

Se uma unidade de disco utilizada para armazenamento sofre uma falha de hardware, é segura [apagados ou destruído](https://microsoft.com/trustcenter/privacy/you-own-your-data) antes de Microsoft retorna para o fabricante para substituição ou a reparação. Os dados na unidade são substituídos para garantir que os dados não podem ser recuperados por qualquer meio.

## <a name="compute-isolation"></a>Isolamento de computação
O Microsoft Azure fornece vários serviços baseados na nuvem computação que incluem uma ampla seleção de instâncias de computação e serviços que podem aumenta e reduz automaticamente para satisfazer as necessidades do seu aplicativo ou a empresa. Estes instância de computação e o serviço oferecem isolamento em vários níveis para proteger os dados sem sacrificar a flexibilidade na configuração essa demanda de clientes.

### <a name="isolated-virtual-machine-sizes"></a>Tamanhos de Máquina Virtual em ambiente isolado
A Computação do Azure oferece tamanhos de máquinas virtuais que são Isolados para um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que exigem um elevado grau de isolamento de outros clientes para cargas de trabalho que envolvem elementos como os requisitos de conformidade e regulamentares.  Os clientes também podem optar por subdividir os recursos dessas máquinas de virtuais isoladas usando [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Utilizar um tamanho de isolado garante que sua máquina virtual será a apenas um em execução nessa instância do servidor específico.  As ofertas de máquina de virtual isolado atuais incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2

Pode saber mais sobre cada tamanho isolado disponível [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Isolamento de sistema operacional de Hyper-V & raiz entre a máquina virtual raiz e máquinas virtuais convidadas
Plataforma de computação do Azure baseia-se a virtualização de computadores, que significa que todo o código de cliente seja executado numa máquina virtual de Hyper-V. Em cada nó do Azure (ou ponto final de rede), há um hipervisor que é executado diretamente sobre o hardware e divide um nó num número variável de convidado máquinas virtuais (VMs).


![Isolamento de sistema operacional de Hyper-V & raiz entre a máquina virtual raiz e máquinas virtuais convidadas](./media/azure-isolation/azure-isolation-fig4.jpg)


Cada nó também tem um especial raiz VM, o que é executado o sistema operativo anfitrião. Um limite crítico é o isolamento da VM a partir das máquinas virtuais convidadas e das máquinas virtuais convidadas umas das outras, gerenciadas pelo hipervisor e o sistema operacional raiz raiz. O emparelhamento de hipervisor/sistema operacional raiz tira partido de décadas da Microsoft de experiência de segurança do sistema operativo e aprendido mais recentemente com Hyper-V da Microsoft, para oferecer forte isolamento de máquinas virtuais convidadas.

A plataforma Azure utiliza um ambiente virtualizado. Instâncias de utilizador funcionam como máquinas virtuais autónomas que não têm acesso a um servidor de anfitrião físico.

O hipervisor do Azure funciona como um kernel micro e passa todos os pedidos de acesso de hardware das máquinas de virtuais de convidado para o anfitrião para processamento através de uma interface de memória partilhada denominada VMBus. Isto impede os utilizadores de obterem acesso de leitura/escrita/execução não processado ao sistema e atenua o risco de partilha de recursos do sistema.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Algoritmo de colocação de VM avançada e de proteção contra ataques de canal de lado
Qualquer ataque entre-VM envolve dois passos: colocar uma VM controlada pelo adversário no mesmo anfitrião como um da vítima VMs e, em seguida, que viola o limite de isolamento para roubar informações confidenciais vítima ou para afetar o desempenho para ganância ou vandalism. O Microsoft Azure fornece proteção em ambas as etapas usando um algoritmo de colocação de VM e proteção contra todos os ataques de canal de lado conhecidos, incluindo VMs de vizinhos ruidosos avançados.

### <a name="the-azure-fabric-controller"></a>O controlador de malha do Azure
O Azure Fabric Controller é responsável por atribuir recursos de infraestrutura para cargas de trabalho de inquilino e gere comunicações unidirecionais do anfitrião para máquinas virtuais. O algoritmo de colocação de VM do controlador de recursos de infraestrutura do Azure é altamente sofisticados e praticamente impossível prever como o nível do anfitrião físico.

![O controlador de malha do Azure](./media/azure-isolation/azure-isolation-fig5.png)

O hipervisor do Azure impõe a separação de processo e de memória entre máquinas virtuais e, em segurança encaminha o tráfego de rede para inquilinos do SO convidado. Isso elimina a possibilidade de e lado ataque de canal ao nível da VM.

No Azure, a máquina virtual de raiz é especial: ele é executado um sistema operacional fortalecidas chamado pelo sistema operacional raiz que aloja um agente de recursos de infraestrutura (FA). FAs são utilizados por sua vez para gerir agentes convidados (GA) em sistemas operacionais convidados em VMs do cliente. FAs também gerenciam nós de armazenamento.

A coleção de hipervisor do Azure, sistema operacional/FA de raiz e cliente VMs/gás é composto por um nó de computação. FAs são geridos pelo controlador de malha (FC), que existe fora de nós de computação e armazenamento (os clusters de computação e armazenamento são geridos pelo FCs separado). Se um cliente de atualizações de ficheiro de configuração de seu aplicativo enquanto ele estiver em execução, o FC se comunica com o FA, que, em seguida, entra em contacto com gás, notificar que a aplicação da alteração de configuração. Em caso de falha de hardware, o FC automaticamente localiza hardware disponível e reinicie a VM existe.

![Controlador de malha do Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

Comunicação entre um controlador de malha e um agente é unidirecional. O agente implementa um serviço protegido por SSL que responde apenas às solicitações do controlador. Ele não pode iniciar conexões com o controlador ou de outros nós internos privilegiados. O FC trata todas as respostas como se estivessem não fidedignos.


![Controlador de malha](./media/azure-isolation/azure-isolation-fig7.png)

Estende o isolamento da VM raiz de máquinas virtuais convidadas e das máquinas virtuais convidadas umas das outras. Também estão isolados de nós de armazenamento para uma maior proteção nós de computação.


O hipervisor e o anfitrião do que sistema operacional forneça o pacote de rede - filtros para ajudar a garantir que as máquinas de virtuais não fidedignas não é possível gerar tráfego falsificado ou receber tráfego não endereçado a elas, direcionar o tráfego para pontos finais de infraestrutura protegidos, ou envio/receção tráfego de difusão inadequado.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Regras adicionais configuradas pelo agente de controlador de recursos de infraestrutura para isolar a VM
Por predefinição, todo o tráfego é bloqueado quando é criada uma máquina virtual e, em seguida, o agente de controlador de recursos de infraestrutura configura o filtro de pacotes para adicionar regras e exceções para permitir tráfego autorizado.

Existem duas categorias de regras que são programadas:

-   **Regras de configuração ou a infraestrutura de máquina:** Por predefinição, todas as comunicações é bloqueada. Existem exceções para permitir que uma máquina virtual para enviar e receber tráfego DHCP e DNS. Máquinas virtuais também podem enviar tráfego para a internet "pública" e enviar o tráfego para outras máquinas virtuais na mesma rede Virtual do Azure e o servidor de ativação do SO. Lista as máquinas virtuais de destinos de saída permitidos não inclui sub-redes de router do Azure, a gestão do Azure e outras propriedades da Microsoft.

-   **Ficheiro de configuração de função:** Isso define a entrada acesso listas de controle (ACLs) com base no modelo de serviço do inquilino.

### <a name="vlan-isolation"></a>Isolamento de VLAN
Existem três VLANs em cada cluster:

![Isolamento de VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   Nós não fidedignos do cliente de interconexões de VLAN principal –

-   A VLAN de FC – contém os FCs e sistemas de suporte

-   O dispositivo VLAN – contém rede fidedigna e outros dispositivos de infraestrutura

Comunicação tem permissão de FC VLAN para a VLAN principal, mas não pode ser iniciada a partir de VLAN principal para a VLAN de FC. Comunicação também é bloqueada a partir de VLAN principal para o dispositivo VLAN. Isso garante que, mesmo se um nó de execução do código de cliente for comprometido, não é possível ataque nós no FC ou dispositivo VLANs.

## <a name="storage-isolation"></a>Isolamento de armazenamento
### <a name="logical-isolation-between-compute-and-storage"></a>Isolamento lógico entre a computação e armazenamento
Como parte do seu design fundamental, Microsoft Azure separa a computação baseada em VM do armazenamento. Essa separação permite que computação e armazenamento para dimensionar de forma independente, tornando mais fácil fornecer isolamento e de vários inquilinos.

Por conseguinte, armazenamento do Azure é executado em hardware separado sem conectividade de rede para a computação do Azure, exceto logicamente. [Isso](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) significa que quando um disco virtual é criado, espaço em disco não está alocado para a sua capacidade de toda. Em vez disso, é criada uma tabela que mapeie endereços no disco virtual para áreas no disco físico e que a tabela está inicialmente vazia. **Na primeira vez que um cliente escreve dados no disco virtual, espaço no disco físico é alocado, e um ponteiro para ela é colocado na tabela.**
### <a name="isolation-using-storage-access-control"></a>Controlo de acesso de armazenamento através do isolamento
**Controlo de acesso no armazenamento do Azure** tem um modelo de controle de acesso simples. Cada subscrição do Azure pode criar uma ou mais contas de armazenamento. Cada conta de armazenamento tem uma única chave secreta, que é utilizada para controlar o acesso a todos os dados nessa conta de armazenamento.

![Controlo de acesso de armazenamento através do isolamento](./media/azure-isolation/azure-isolation-fig9.png)

**Acesso a dados de armazenamento do Azure (incluindo tabelas)** pode ser controlada por meio de um [SAS (assinatura de acesso partilhado)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, que concede acesso de âmbito. A SAS é criada através de um modelo de consulta (URL), tem sessão iniciado com o [SAK (chave de conta de armazenamento)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Que [assinado URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) puder ser dado ao outro processo (que é, delegado), que pode, em seguida, preencha os detalhes da consulta e fazer o pedido de serviço de armazenamento. Uma SAS permite-lhe conceder acesso com base no tempo para os clientes sem as revelar a chave secreta da conta de armazenamento.

A SAS significa que podemos pode conceder que um cliente permissões limitadas, a objetos na nossa conta de armazenamento para um determinado período de tempo e com um conjunto especificado de permissões. Podemos pode conceder estas permissões limitadas sem ter de partilhar as chaves de acesso da conta.

### <a name="ip-level-storage-isolation"></a>Isolamento de armazenamento o nível de IP
Pode estabelecer firewalls e definir um intervalo de endereços IP para os seus clientes fidedignos. Com um intervalo de endereços IP, apenas os clientes que tenham um endereço IP no intervalo definido podem ligar ao [armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-security-guide).

Dados de armazenamento IP podem ser protegidos contra utilizadores não autorizados por meio de um mecanismo de rede que serve para alocar um túnel dedicado ou dedicado de tráfego para o armazenamento IP.

### <a name="encryption"></a>Encriptação
O Azure oferece os seguintes tipos de encriptação para proteger os dados:
-   Encriptação em trânsito

-   Encriptação inativa

#### <a name="encryption-in-transit"></a>Encriptação em trânsito
Encriptação em trânsito é um mecanismo de proteção de dados quando ele é transmitido através de redes. Com o armazenamento do Azure, pode proteger os dados com:

-   [Encriptação de nível de transporte](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), por exemplo, HTTPS, quando a transferência de dados dentro ou fora do armazenamento do Azure.

-   [Ativar encriptação](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como a encriptação SMB 3.0 para partilhas de ficheiros do Azure.

-   [Encriptação do lado do cliente](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para encriptar os dados antes de é transferido para o armazenamento e para descriptografar os dados depois de serem transferido para fora do armazenamento.

#### <a name="encryption-at-rest"></a>Encriptação inativa
Para muitas organizações, [encriptação de dados em repouso](https://docs.microsoft.com/azure/security/azure-isolation) é um passo obrigatório em direção à soberania de dados, conformidade e privacidade de dados. Existem três recursos do Azure que fornecem criptografia de dados que estão "Inativos":

-   [Encriptação do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) permite-lhe pedir que o serviço de armazenamento encriptar dados automaticamente quando a escrevê-lo ao armazenamento do Azure.

-   [Encriptação do lado do cliente](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) também fornece a funcionalidade de encriptação em repouso.

-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) permite que criptografe os discos de SO e discos de dados utilizados pela máquina virtual IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) para máquinas virtuais (VMs) ajuda-o a requisitos de conformidade e de segurança organizacionais ao encriptar os discos da VM (incluindo os discos de dados e de arranque) com chaves e políticas de controlo no [chave do Azure Cofre](https://azure.microsoft.com/services/key-vault/).

A solução de encriptação de disco para o Windows baseia [Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx), e a solução do Linux é baseada em [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

A solução suporta os seguintes cenários para IaaS VMs quando forem ativadas no Microsoft Azure:
-   Integração com o Cofre de chaves do Azure

-   Escalão Standard VMs: A, D, DS, G, GS e assim por diante, VMs de IaaS de série

-   Ativar a encriptação em VMs de IaaS de Linux e Windows

-   A desativação da encriptação no SO e dados de unidades para VMs de IaaS do Windows

-   Desativar a encriptação em unidades de dados para VMs de IaaS Linux

-   Ativar a encriptação em VMs de IaaS que estão a executar o SO de cliente do Windows

-   Ativar a encriptação em volumes com caminhos de montagem

-   Ativar a encriptação em VMs do Linux que estão configurados com disco repartição (RAID) utilizando [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Ativar a encriptação em VMs do Linux usando [LVM (Gestor de volumes lógicos)](https://msdn.microsoft.com/library/windows/desktop/bb540532) para discos de dados

-   Ativar a encriptação em VMs do Windows que são configuradas através de espaços de armazenamento

-   São suportadas todas as regiões públicas do Azure

A solução não suporta os seguintes cenários, recursos e tecnologia na versão:

-   VMs de IaaS de escalão básico

-   A desativação da encriptação numa unidade do sistema operacional para VMs de IaaS Linux

-   VMs de IaaS que são criadas com o método de criação de VM clássico

-   Integração com o seu serviço de gestão de chaves no local

-   Ficheiros do Azure (sistema de ficheiros partilhados), o sistema de ficheiros de rede (NFS), volumes dinâmicos e VMs do Windows que estão configurados com sistemas RAID baseados em software

## <a name="sql-azure-database-isolation"></a>Isolamento de base de dados do SQL Azure
A Base de Dados SQL é um serviço de bases de dados relacionais na cloud da Microsoft baseado no motor Microsoft SQL Server líder de mercado e com capacidade para processar cargas de trabalho críticas para missões. Base de dados SQL oferece o isolamento de dados previsível ao nível da conta, geografia / região com base e com base no sistema de rede — tudo com quase sem administração.

### <a name="sql-azure-application-model"></a>Modelo de aplicativo do SQL Azure

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) base de dados é um serviço de base de dados relacional com base na cloud, criado sobre as tecnologias do SQL Server. Ele fornece um serviço de base de dados de elevada disponibilidade, dimensionáveis e de multilocação hospedado pela Microsoft na cloud.

De um aplicativo ponto de vista de SQL Azure fornece a seguinte hierarquia: Cada nível tem um-para-muitos contenção de níveis abaixo.

![Modelo de aplicativo do SQL Azure](./media/azure-isolation/azure-isolation-fig10.png)

A conta e subscrição são conceitos de plataforma do Microsoft Azure para associar a faturação e gestão.

Servidores lógicos e bases de dados são conceitos específicos do SQL Azure e são geridos com o SQL Azure, desde o OData e TSQL interfaces ou através do portal do SQL Azure que integradas no portal do Azure.

Servidores do SQL Azure não são física ou instâncias de VM, em vez disso, eles são coleções de bases de dados, compartilhamento de políticas de gestão e segurança, que são armazenadas na então chamado "mestre lógico" base de dados.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Bases de dados mestras lógicas incluem:

-   Inícios de sessão SQL utilizados para ligar ao servidor

-   Regras da firewall

Faturas relacionadas com a utilização informações de e para SQL Azure bases de dados do mesmo servidor lógico não são garantidos ser na mesma instância física num cluster do SQL Azure, em vez disso, aplicativos tem de fornecer o nome de base de dados de destino quando se liga.

Da perspectiva do cliente, um servidor lógico é criado numa região geo-gráficos, embora na criação do servidor ocorre em um dos clusters na região.

### <a name="isolation-through-network-topology"></a>Isolamento através da topologia de rede

Quando um servidor lógico é criado e o respetivo nome DNS está registado, os nomes do DNS aponta para o endereço "VIP do Gateway" então, chamado no Centro de dados específico em que o servidor foi colocado.

Por trás do VIP (endereço IP virtual), temos uma coleção de serviços do gateway sem monitoração de estado. Em geral, gateways envolvem quando existe coordenação necessária entre várias origens de dados (base de dados mestra, base de dados do utilizador, etc.). Serviços do gateway de implementam o seguinte:
-   **Utilização do proxy ligação TDS.** Isto inclui a localização da base de dados do utilizador no cluster back-end, implementar a sequência de início de sessão e, em seguida, encaminhar os pacotes de recebido para o back-end e vice-versa.

-   **Gestão de bases de dados.** Inclui a implementação de uma coleção de fluxos de trabalho para fazer operações de banco de dados CREATE/ALTER/DROP. As operações de base de dados podem ser invocadas por detecção de TDS de pacotes ou as APIs de OData explícita.

-   Operações de CREATE/ALTER/DROP. o início de sessão/utilizador

-   Operações de gestão do servidor lógico através da API de OData

![Isolamento através da topologia de rede](./media/azure-isolation/azure-isolation-fig12.png)

O escalão por trás os gateways é chamado de "back-end". Isso é onde todos os dados são armazenados de forma altamente disponível. Cada conjunto de dados-se que pertencem a "partição" ou "failover unidade", de cada um deles ter, pelo menos, três réplicas. As réplicas são armazenadas e replicadas pelo mecanismo do SQL Server e geridas por um sistema de ativação pós-falha, muitas vezes, referido como "malha".

Em geral, o sistema de back-end não comunicam com outros sistemas de saída como precaução de segurança. Isso está reservado para os sistemas na camada de front-end (gateway). As máquinas de camada de gateway têm privilégios nas máquinas de back-end para minimizar a superfície de ataque como um mecanismo de defesa em profundidade limitados.

### <a name="isolation-by-machine-function-and-access"></a>Isolamento através da função de máquina e acesso
SQL Azure (é composto por serviços em execução nas funções de máquina diferente. SQL Azure é dividida, em "" front-end (Gateway/gestão) e "back-end" base de dados na Cloud, ambientes, com o princípio geral de vai apenas do tráfego no back-end e não horizontalmente. O ambiente de front-end pode comunicar com o mundo exterior dos outros serviços e, em geral, possui apenas permissões limitadas no back-end (o suficiente para chamar os pontos de entrada tem de invocar).

## <a name="networking-isolation"></a>Isolamento de rede
Implementação do Azure tem várias camadas de isolamento de rede. O diagrama seguinte mostra várias camadas do Azure fornece aos clientes de isolamento de rede. Essas camadas são nativo na própria plataforma do Azure e os recursos definidos pelo cliente. Entrada da Internet, o DDoS do Azure fornece isolamento contra ataques em grande escala no Azure. A próxima camada de isolamento é definida pelo cliente endereços IP públicos (pontos de extremidade), que são utilizados para determinar qual o tráfego pode passar através do serviço de nuvem para a rede virtual. Nativo virtuais do Azure garante de isolamento de rede completo isolamento de todas as outras redes, e que o tráfego apenas flui através de caminhos de utilizador configurado e métodos. Estes caminhos e os métodos são a próxima camada, onde os NSGs, UDR e aplicações virtuais de rede podem ser utilizadas para criar limites de isolamento para proteger as implementações de aplicações na rede protegida.

![Isolamento de rede](./media/azure-isolation/azure-isolation-fig13.png)

**Isolamento de tráfego:** R [rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) é o limite de isolamento de tráfego na plataforma do Azure. Máquinas virtuais (VMs) numa rede virtual não consegue comunicar diretamente para as VMs numa rede virtual diferente, mesmo se ambas as redes virtuais são criadas ao mesmo cliente. Isolamento é uma propriedade crítica que garante a VMs de cliente e comunicação são mantidos privada numa rede virtual.

[Sub-rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) oferece uma camada adicional de isolamento com na rede virtual com base no intervalo de IP. Endereços IP na rede virtual, pode dividir uma rede virtual em várias sub-redes para a organização e segurança. As VMs e as instâncias de função de PaaS implementadas em sub-redes (nas mesmas ou em diferentes) dentro de uma VNet podem comunicar entre si sem qualquer configuração adicional. Também pode configurar [grupo de segurança de rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) para permitir ou negar o tráfego de rede para uma instância de VM com base em regras configuradas na lista de controlo de acesso (ACL) de NSG. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede.

## <a name="next-steps"></a>Próximos Passos

- [Opções de isolamento de rede para máquinas no Windows redes virtuais do Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Isto inclui o cenário clássico de front-end e back-end em que máquinas numa rede de back-end específico ou a sub-rede permitirão somente determinados clientes ou outros computadores ligar a um determinado ponto de extremidade com base numa lista de permissões de endereços IP.

- [Isolamento de computação](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

O Microsoft Azure fornece um várias com base na cloud serviços de informática que incluem uma ampla seleção de instâncias de computação e serviços que podem aumenta e reduz automaticamente para satisfazer as necessidades do seu aplicativo ou a empresa.

- [Isolamento de armazenamento](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure separa a computação de cliente baseada em VM do armazenamento. Essa separação permite que computação e armazenamento para dimensionar de forma independente, tornando mais fácil fornecer isolamento e de vários inquilinos. Por conseguinte, armazenamento do Azure é executado em hardware separado sem conectividade de rede para a computação do Azure, exceto logicamente. Executam todos os pedidos através de HTTP ou HTTPS com base na escolha do cliente.

