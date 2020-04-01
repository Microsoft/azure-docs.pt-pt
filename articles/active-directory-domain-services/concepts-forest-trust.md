---
title: Como os trusts funcionam para os Serviços de Domínio Azure AD [ Microsoft Docs
description: Saiba mais sobre como a confiança florestal funciona com os Serviços de Domínio Da AD Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 903881a1d15c1f043e381f50e5b69d661cd08192
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476433"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Como as relações de confiança funcionam para as florestas de recursos em Serviços de Domínio de Diretório Ativo Azure

Os Serviços de Domínio de Diretório Ativo (AD DS) fornecem segurança em vários domínios ou florestas através de relações de domínio e de confiança florestal. Antes que a autenticação possa ocorrer através de fundos fiduciários, o Windows deve primeiro verificar se o domínio que está a ser solicitado por um utilizador, computador ou serviço tem uma relação de confiança com o domínio da conta que solicita.

Para verificar esta relação de confiança, o sistema de segurança do Windows calcula uma trajetória de confiança entre o controlador de domínio (DC) para o servidor que recebe o pedido e um DC no domínio da conta de solicitação.

Os mecanismos de controlo de acesso fornecidos pela AD DS e pelo modelo de segurança distribuído pelo Windows proporcionam um ambiente para o funcionamento de fundos de domínio e florestais. Para que estes fundos funcionem corretamente, cada recurso ou computador deve ter um caminho de confiança direto para um DC no domínio em que está localizado.

A via fidedigna é implementada pelo serviço Net Logon utilizando uma ligação autenticada de chamada de procedimento remoto (RPC) à autoridade de domínio fidedigno. Um canal seguro também se estende a outros domínios AD DS através de relações de confiança interdomínio. Este canal seguro é utilizado para obter e verificar informações de segurança, incluindo identificadores de segurança (SIDs) para utilizadores e grupos.

## <a name="trust-relationship-flows"></a>Fluxos de relacionamento de confiança

O fluxo de comunicações seguras sobre os trusts determina a elasticidade de um fundo. A forma como cria ou configura um fundo determina até que ponto a comunicação se estende dentro ou através das florestas.

O fluxo de comunicação sobre os fundos é determinado pela direção da confiança. Os fundos podem ser de ida ou de duas vias, e podem ser transitórios ou não transitórios.

O diagrama seguinte mostra que todos os domínios da *Árvore 1* e da *Árvore 2* têm relações de confiança transitórias por defeito. Como resultado, os utilizadores na *Árvore 1* podem aceder a recursos em domínios na *Árvore 2* e os utilizadores na Árvore *1* podem aceder aos recursos na *Árvore 2,* quando as permissões adequadas são atribuídas ao recurso.

![Diagrama de relações de confiança entre duas florestas](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Confianças de ida e de dois sentidos

As relações de confiança permitem o acesso aos recursos, quer de ida ou de duas vias.

Uma confiança unidirecional é um caminho de autenticação unidirecional criado entre dois domínios. Numa confiança de sentido único entre o *Domínio A* e o *Domínio B,* os utilizadores do *Domínio A* podem aceder aos recursos no *Domínio B*. No entanto, os utilizadores do *Domínio B* não podem aceder a recursos no *Domínio A*.

Alguns fundos de sentido único podem ser não transitórios ou transitórios dependendo do tipo de confiança que está a ser criada.

Numa confiança bidirecional, *o Domínio A* confia no Domínio *B* e no *Domínio B* confia no Domínio *A*. Esta configuração significa que os pedidos de autenticação podem ser passados entre os dois domínios em ambas as direções. Algumas relações bidirecionais podem ser não transitivas ou transitivas dependendo do tipo de confiança que está a ser criada.

Todos os fundos de domínio numa floresta dS a.C. são fundos transitórios de duas vias. Quando um novo domínio infantil é criado, uma confiança transitiva de duas vias é automaticamente criada entre o novo domínio infantil e o domínio dos pais.

### <a name="transitive-and-non-transitive-trusts"></a>Fidedignidades transitivas e não transitivas

A transitividade determina se um fundo pode ser estendido fora dos dois domínios com os quais foi formado.

* Uma confiança transitiva pode ser usada para estender as relações de confiança com outros domínios.
* Uma confiança não transitória pode ser usada para negar relações de confiança com outros domínios.

Cada vez que se cria um novo domínio numa floresta, uma relação de confiança bidirecional é automaticamente criada entre o novo domínio e o seu domínio-mãe. Se os domínios infantis forem adicionados ao novo domínio, o caminho da confiança flui para cima através da hierarquia do domínio, estendendo o caminho inicial de confiança criado entre o novo domínio e o seu domínio principal. As relações transitórias de confiança fluem para cima através de uma árvore de domínio à medida que é formada, criando confiança transitiva entre todos os domínios da árvore de domínio.

Os pedidos de autenticação seguem estes caminhos de confiança, para que as contas de qualquer domínio da floresta possam ser autenticadas por qualquer outro domínio da floresta. Com um único processo de logon, as contas com as permissões adequadas podem aceder a recursos em qualquer domínio da floresta.

## <a name="forest-trusts"></a>Confianças de floresta

Os fundos florestais ajudam-no a gerir uma infraestrutura AD DS segmentada e a apoiar o acesso a recursos e outros objetos em várias florestas. Os fundos florestais são úteis para prestadores de serviços, empresas em fusão ou aquisições, extranets de negóciocolabora, e empresas que procuram uma solução para a autonomia administrativa.

Usando fundos florestais, você pode ligar duas florestas diferentes para formar uma relação de confiança transitória de ida ou bidirecional. Um fundo florestal permite que os administradores liguem duas florestas aD DS com uma única relação de confiança para fornecer uma experiência de autenticação e autorização perfeita em todas as florestas.

Uma confiança florestal só pode ser criada entre um domínio de raiz florestal numa floresta e um domínio de raiz florestal em outra floresta. Os fundos florestais só podem ser criados entre duas florestas e não podem ser implicitamente estendidos a uma terceira floresta. Este comportamento significa que se um fundo florestal é criado entre a *Floresta 1* e a *Floresta 2*, e outra confiança florestal é criada entre a *Floresta 2* e a Floresta *3*, a *Floresta 1* não tem uma confiança implícita com a Floresta *3*.

O diagrama seguinte mostra duas relações de confiança florestal separadas entre três florestas DS numa única organização.

![Diagrama da floresta confia nas relações dentro de uma única organização](./media/concepts-forest-trust/forest-trusts.png)

Esta configuração de exemplo proporciona o seguinte acesso:

* Os utilizadores na *Floresta 2* podem aceder a recursos em qualquer domínio na *Floresta 1* ou Na *Floresta 3*
* Os utilizadores da *Floresta 3* podem aceder a recursos em qualquer domínio na *Floresta 2*
* Os utilizadores da *Floresta 1* podem aceder a recursos em qualquer domínio na *Floresta 2*

Esta configuração não permite que os utilizadores na *Floresta 1* acedam a recursos na *Floresta 3* ou vice-versa. Para permitir que os utilizadores da *Floresta 1* e da *Floresta 3* partilhem recursos, deve ser criada uma confiança transitória bidirecional entre as duas florestas.

Se for criada uma confiança florestal de sentido único entre duas florestas, os membros da floresta fidedigna podem utilizar recursos localizados na floresta de confiança. No entanto, a confiança opera numa única direção.

Por exemplo, quando se cria uma confiança florestal única entre a *Floresta 1* (a floresta fidedigna) e a *Floresta 2* (a floresta de confiança):

* Os membros da *Floresta 1* podem aceder aos recursos localizados na *Floresta 2*.
* Os membros da *Floresta 2* não conseguem aceder aos recursos localizados na *Floresta 1* usando a mesma confiança.

> [!IMPORTANT]
> A floresta de recursos da Azure AD Domain Services apenas suporta uma confiança florestal de sentido único para o Diretório Ativo no local.

### <a name="forest-trust-requirements"></a>Requisitos de confiança florestal

Antes de criar um fundo florestal, precisa verificar se tem a infraestrutura correta do Sistema de Nome de Domínio (DNS) no lugar. Os fundos florestais só podem ser criados quando uma das seguintes configurações de DNS estiver disponível:

* Um servidor DNS raiz único é o servidor DNS raiz para ambos os espaços de nome DNS florestais - a zona raiz contém delegações para cada um dos espaços de nome DNS e as pistas de raiz de todos os servidores DNS incluem o servidor DNS raiz.
* Quando não houver um servidor DNS de raiz partilhada, e os servidores DNS de raiz para cada espaço de nome DNS da floresta usam avançados dNS condicionados para cada espaço de nome DNS para encaminhar consultas para nomes no outro espaço de nome.

    > [!IMPORTANT]
    > A floresta de recursos azure AD Domain Services deve utilizar esta configuração DNS. Hospedar um espaço de nome DNS que não seja o espaço de nome dNS da floresta de recursos não é uma característica dos Serviços de Domínio Azure AD. Os avançados condicional é a configuração adequada.

* Quando não houver um servidor DNS de raiz partilhada, e os servidores DNS de raiz para cada espaço de nome DNS da floresta são usar zonas secundárias DNS são configuradas em cada espaço de nome DNS para encaminhar consultas para nomes no outro espaço de nome.

Para criar um fundo florestal, você deve ser um membro do grupo Domain Admins (no domínio da raiz da floresta) ou do grupo Deadministradores Empresariais em Diretório Ativo. A cada fundo é atribuída uma senha que os administradores de ambas as florestas devem conhecer. Membros da Enterprise Admins em ambas as florestas podem criar os fidedignos em ambas as florestas ao mesmo tempo e, neste cenário, uma palavra-passe criptograficamente aleatória é automaticamente gerada e escrita para ambas as florestas.

O fundo florestal de saída para os Serviços de Domínio Azure AD é criado no portal Azure. Não se cria manualmente a confiança com o próprio domínio gerido. O fundo florestal de entrada deve ser configurado por um utilizador com os privilégios anteriormente anotados no Diretório Ativo no local.

## <a name="trust-processes-and-interactions"></a>Processos de confiança e interações

Muitas transações inter-domínio e interflorestais dependem de fundos de domínio ou floresta, a fim de completar várias tarefas. Esta secção descreve os processos e interações que ocorrem à medida que os recursos são acedidos através de fundos fiduciários e referenciações de autenticação são avaliados.

### <a name="overview-of-authentication-referral-processing"></a>Visão geral do processamento de referência de autenticação

Quando um pedido de autenticação é referido a um domínio, o controlador de domínio nesse domínio deve determinar se existe uma relação de confiança com o domínio a partir do qual o pedido provém. A direção da confiança e se a confiança é transitiva ou não transitiva também deve ser determinada antes de autenticar o utilizador para aceder aos recursos no domínio. O processo de autenticação que ocorre entre domínios fidedignos varia de acordo com o protocolo de autenticação em uso. Os protocolos Kerberos V5 e NTLM processam referências para autenticação a um domínio de forma diferente

### <a name="kerberos-v5-referral-processing"></a>Processamento de referência Kerberos V5

O protocolo de autenticação Kerberos V5 está dependente do serviço Net Logon em controladores de domínio para autenticação e autorização do cliente. O protocolo Kerberos liga-se a um Centro de Distribuição de Chaves online (KDC) e à loja de contas Ative Directory para bilhetes de sessão.

O protocolo Kerberos também utiliza fundos para serviços de concessão de bilhetes transversais (TGS) e para validar certificados de atribuição de privilégios (PACs) através de um canal seguro. O protocolo Kerberos realiza a autenticação transversal apenas com domínios do sistema operativo kerberos sem windows, como um reino mit Kerberos e não precisa de interagir com o serviço Net Logon.

Se o cliente utilizar kerberos V5 para autenticação, solicita um bilhete para o servidor no domínio alvo a partir de um controlador de domínio no seu domínio de conta. O Kerberos KDC atua como um intermediário de confiança entre o cliente e o servidor e fornece uma chave de sessão que permite que as duas partes se atentuquem mutuamente. Se o domínio alvo for diferente do domínio atual, o KDC segue um processo lógico para determinar se um pedido de autenticação pode ser referido:

1. O domínio atual é confiado diretamente pelo domínio do servidor que está a ser solicitado?
    * Se sim, envie ao cliente um encaminhamento para o domínio solicitado.
    * Se não, vá para o próximo passo.

2. Existe uma relação de confiança transitória entre o domínio atual e o próximo domínio no caminho da confiança?
    * Se sim, envie ao cliente uma referência para o próximo domínio no caminho da confiança.
    * Se não, envie ao cliente uma mensagem negada por sessão.

### <a name="ntlm-referral-processing"></a>Processamento de referência NTLM

O protocolo de autenticação NTLM está dependente do serviço Net Logon em controladores de domínio para informação de autenticação e autorização do cliente. Este protocolo autentica clientes que não utilizam a autenticação Kerberos. A NTLM utiliza fundos fiduciários para passar pedidos de autenticação entre domínios.

Se o cliente utilizar a NTLM para autenticação, o pedido inicial de autenticação vai diretamente do cliente para o servidor de recursos no domínio alvo. Este servidor cria um desafio ao qual o cliente responde. Em seguida, o servidor envia a resposta do utilizador a um controlador de domínio no seu domínio de conta de computador. Este controlador de domínio verifica a conta de utilizador na base de dados das suas contas de segurança.

Se a conta não existir na base de dados, o controlador de domínio determina se deve efetuar a autenticação pass-through, reencaminhar o pedido ou negar o pedido utilizando a seguinte lógica:

1. O domínio atual tem uma relação de confiança direta com o domínio do utilizador?
    * Se sim, o controlador de domínio envia as credenciais do cliente para um controlador de domínio no domínio do utilizador para autenticação pass-through.
    * Se não, vá para o próximo passo.

2. O domínio atual tem uma relação de confiança transitiva com o domínio do utilizador?
    * Se sim, passe o pedido de autenticação para o próximo domínio no caminho da confiança. Este controlador de domínio repete o processo verificando as credenciais do utilizador na sua própria base de dados de contas de segurança.
    * Se não, envie ao cliente uma mensagem negada por sessão.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Processamento baseado em Kerberos de pedidos de autenticação sobre fundos florestais

Quando duas florestas são ligadas por um fundo florestal, os pedidos de autenticação feitos utilizando os protocolos Kerberos V5 ou NTLM podem ser encaminhados entre florestas para fornecer acesso aos recursos em ambas as florestas.

Quando um fundo florestal é estabelecido pela primeira vez, cada floresta recolhe todos os espaços de nome fidedignos na sua floresta parceira e armazena a informação num objeto de [domínio de confiança.](#trusted-domain-object) Os espaços de nome fidedignos incluem nomes de árvores de domínio, sufixos de nome principal do utilizador (UPN), sufixos de nome principal de serviço (SPN) e espaços de identificação de segurança (SID) usados na outra floresta. Os objetos TDO são replicados para o catálogo global.

Antes que os protocolos de autenticação possam seguir o caminho de confiança florestal, o nome principal de serviço (SPN) do computador de recurso deve ser resolvido para um local na outra floresta. Um SPN pode ser um dos seguintes:

* O nome DNS de um hospedeiro.
* O nome DNS de um domínio.
* O nome distinto de um objeto de ponto de ligação de serviço.

Quando uma estação de trabalho numa floresta tenta aceder a dados de um computador de recurso noutra floresta, o processo de autenticação Kerberos contacta o controlador de domínio para um bilhete de serviço para a SPN do computador de recurso. Uma vez que o controlador de domínio questiona o catálogo global e determina que o SPN não está na mesma floresta que o controlador de domínio, o controlador de domínio envia uma referência para o seu domínio principal de volta para a estação de trabalho. Nessa altura, a estação de trabalho consulta o domínio principal do bilhete de serviço e continua a seguir a cadeia de referenciação até chegar ao domínio onde o recurso está localizado.

O diagrama e os passos seguintes fornecem uma descrição detalhada do processo de autenticação Kerberos que é usado quando computadores que executam o Windows tentam aceder a recursos de um computador localizado em outra floresta.

![Diagrama do processo Kerberos sobre uma confiança florestal](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *User1* inicia sessão no *Workstation1* utilizando credenciais do domínio *europe.tailspintoys.com.* O utilizador tenta então aceder a um recurso partilhado no *FileServer1* localizado na floresta *usa.wingtiptoys.com.*

2. *Workstation1* contacta o Kerberos KDC num controlador de domínio no seu domínio, *ChildDC1,* e solicita um bilhete de serviço para o *FileServer1* SPN.

3. *A ChildDC1* não encontra o SPN na sua base de dados de domínio e questiona o catálogo global para ver se algum domínio na floresta *tailspintoys.com* contém este SPN. Como um catálogo global está limitado à sua própria floresta, o SPN não é encontrado.

    O catálogo global verifica então a sua base de dados para obter informações sobre quaisquer fundos florestais que sejam estabelecidos com a sua floresta. Se for encontrado, compara os sufixos de nome listados no objeto de domínio fidedigno da confiança da floresta (TDO) ao sufixo do alvo SPN para encontrar uma correspondência. Uma vez que um fósforo é encontrado, o catálogo global fornece uma dica de encaminhamento de volta para *ChildDC1*.

    As dicas de encaminhamento ajudam a direcionar os pedidos de autenticação para a floresta de destino. As dicas só são usadas quando todos os canais tradicionais de autenticação, como o controlador de domínio local e, em seguida, o catálogo global, não conseguem localizar um SPN.

4. *A ChildDC1* envia um encaminhamento para o seu domínio principal de volta ao *Workstation1*.

5. *A Workstation1* contacta um controlador de domínio em *ForestRootDC1* (seu domínio principal) para uma referência a um controlador de domínio *(ForestRootDC2*) no domínio da raiz florestal da floresta *wingtiptoys.com.*

6. *Workstation1* contacta *ForestRootDC2* na *floresta wingtiptoys.com* para um bilhete de serviço para o serviço solicitado.

7. *A ForestRootDC2* contacta o seu catálogo global para encontrar o SPN, e o catálogo global encontra uma correspondência para o SPN e envia-o de volta para *ForestRootDC2*.

8. *ForestRootDC2* envia então o encaminhamento para *usa.wingtiptoys.com* de volta ao *Workstation1*.

9. *Workstation1* contacta o KDC em *ChildDC2* e negoceia o bilhete para *o Utilizador1* ter acesso ao *FileServer1*.

10. Uma vez que o *Workstation1* tem um bilhete de serviço, envia o bilhete de serviço para *fileServer1*, que lê as credenciais de segurança do *User1*e constrói um sinal de acesso em conformidade.

## <a name="trusted-domain-object"></a>Objeto de domínio confiável

Cada domínio ou confiança florestal dentro de uma organização é representado por um Objeto de Domínio Confiável (TDO) armazenado no recipiente *system* dentro do seu domínio.

### <a name="tdo-contents"></a>Conteúdo do TDO

A informação contida num TDO varia consoante um TDO tenha sido criado por um fundo de domínio ou por um fundo florestal.

Quando uma confiança de domínio é criada, atributos como o nome de domínio DNS, domínio SID, tipo de confiança, transitividade de confiança, e o nome de domínio recíproco estão representados no TDO. Os TDOs de confiança florestal armazenam atributos adicionais para identificar todos os espaços de nome fidedignos da floresta parceira. Estes atributos incluem nomes de árvores de domínio, sufixos de nome principal do utilizador (UPN), sufixos de nome principal de serviço (SPN) e espaços de nome id de segurança (SID).

Como os fundos são armazenados no Diretório Ativo como TDOs, todos os domínios numa floresta têm conhecimento das relações de confiança que estão em vigor em toda a floresta. Da mesma forma, quando duas ou mais florestas são unidas através de fundos florestais, os domínios das raízes da floresta em cada floresta têm conhecimento das relações de confiança que estão em vigor em todos os domínios em florestas de confiança.

### <a name="tdo-password-changes"></a>Alterações na palavra-passe do TDO

Ambos os domínios de uma relação de confiança partilham uma palavra-passe, que é armazenada no objeto TDO em Ative Directory. Como parte do processo de manutenção da conta, a cada 30 dias o controlador de domínio fidedigno altera a palavra-passe armazenada no TDO. Como todos os fundos de dois sentidos são na verdade dois fundos de sentido único que vão em direções opostas, o processo ocorre duas vezes para fundos de dois sentidos.

Um fundo tem um lado de confiança e de confiança. Do lado de confiança, qualquer controlador de domínio writável pode ser usado para o processo. Do lado da confiança, o emulador pdc executa a alteração da palavra-passe.

Para alterar uma palavra-passe, os controladores de domínio completam o seguinte processo:

1. O emulador principal do controlador de domínio (PDC) no domínio de confiança cria uma nova senha. Um controlador de domínio no domínio fidedigno nunca inicia a alteração da palavra-passe. É sempre iniciado pelo emulador de domínio de confiança pDC.

2. O emulador PDC no domínio de confiança define o campo *OldPassword* do objeto TDO para o campo *NewPassword* atual.

3. O emulador PDC no domínio de confiança define o campo *NewPassword* do objeto TDO para a nova palavra-passe. Manter uma cópia da palavra-passe anterior permite reverter para a palavra-passe antiga se o controlador de domínio no domínio fidedigno não receber a alteração, ou se a alteração não for replicada antes de ser feito um pedido que utilize a nova senha de confiança.

4. O emulador PDC no domínio de confiança faz uma chamada remota para um controlador de domínio no domínio fidedigno pedindo-lhe que desloque a palavra-passe na conta fiduciária para a nova palavra-passe.

5. O controlador de domínio no domínio fidedigno altera a palavra-passe fidedigna para a nova palavra-passe.

6. Em cada lado da confiança, as atualizações são replicadas para os outros controladores de domínio no domínio. No domínio de confiança, a mudança desencadeia uma replicação urgente do objeto de domínio confiável.

A palavra-passe é agora alterada em ambos os controladores de domínio. A replicação normal distribui os objetos TDO aos outros controladores de domínio no domínio. No entanto, é possível que o controlador de domínio no domínio de confiança altere a palavra-passe sem atualizar com sucesso um controlador de domínio no domínio de confiança. Este cenário pode ocorrer porque um canal seguro, que é necessário para processar a mudança de senha, não poderia ser estabelecido. Também é possível que o controlador de domínio no domínio fidedigno possa estar indisponível em algum momento durante o processo e pode não receber a senha atualizada.

Para lidar com situações em que a alteração da palavra-passe não é comunicada com sucesso, o controlador de domínio no domínio de confiança nunca altera a nova palavra-passe a menos que tenha autenticado com sucesso (configurar um canal seguro) usando a nova palavra-passe. Este comportamento é por isso que tanto as palavras-passe antigas como as novas são mantidas no objeto TDO do domínio de confiança.

Uma alteração de palavra-passe não é finalizada até que a autenticação utilizando a palavra-passe seja bem sucedida. A palavra-passe antiga e armazenada pode ser utilizada sobre o canal seguro até que o controlador de domínio no domínio fidedigno receba a nova palavra-passe, permitindo assim um serviço ininterrupto.

Se a autenticação utilizando a nova palavra-passe falhar porque a palavra-passe é inválida, o controlador de domínio fidedigno tenta autenticar usando a palavra-passe antiga. Se autenticar com sucesso com a palavra-passe antiga, retoma o processo de alteração da palavra-passe dentro de 15 minutos.

As atualizações de palavras-passe de confiança precisam de se replicar para os controladores de domínio de ambos os lados da confiança no prazo de 30 dias. Se a palavra-passe de confiança for alterada após 30 dias e um controlador de domínio tiver apenas a senha N-2, não pode utilizar a confiança do lado fiduciário e não pode criar um canal seguro no lado de confiança.

## <a name="network-ports-used-by-trusts"></a>Portas de rede utilizadas por fidedignidades

Como os fundos devem ser implantados através de vários limites da rede, eles podem ter que abranger uma ou mais firewalls. Quando for esse o caso, pode bloquear o tráfego fiduciário através de uma firewall ou abrir portas específicas na firewall para permitir que o tráfego passe.

> [!IMPORTANT]
> Os Serviços de Domínio de Diretório Ativo não suportam restringir o tráfego rPC do Diretório Ativo a portas específicas.

Leia a secção **de versões do Windows Server 2008 e versões posteriores** do Artigo de Suporte da Microsoft [Como configurar uma firewall para domínios de Diretório Ativo e fidedignidades](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) para saber sobre as portas necessárias para um fundo florestal.

## <a name="supporting-services-and-tools"></a>Serviços e ferramentas de apoio

Para apoiar os fundos e a autenticação, são utilizadas algumas funcionalidades adicionais e ferramentas de gestão.

### <a name="net-logon"></a>Net Logon

O serviço Net Logon mantém um canal seguro de um computador baseado no Windows para um DC. Também é usado nos seguintes processos relacionados com a confiança:

* Trust configuração e gestão - Net Logon ajuda a manter senhas de confiança, recolhe informações de confiança e verifica a confiança interagindo com o processo LSA e o TDO.

    Para os fundos florestais, a informação de confiança inclui o registo de Informação sobre a Confiança Florestal *(FTInfo),* que inclui o conjunto de espaços de nome que uma floresta de confiança afirma gerir, anotado com um campo que indica se cada reivindicação é confiada pela floresta de confiança.

* Autenticação – Fornece credenciais de utilizador sobre um canal seguro a um controlador de domínio e devolve os SIDs de domínio e os direitos de utilizador para o utilizador.

* Localização do controlador de domínio – Ajuda a encontrar ou localizar controladores de domínio num domínio ou em domínios.

* Validação pass-through – As credenciais dos utilizadores de outros domínios são processadas pela Net Logon. Quando um domínio de confiança precisa de verificar a identidade de um utilizador, passa as credenciais do utilizador através do Net Logon para o domínio fidedigno para verificação.

* Verificação do Certificado de Atribuição de Privilégios (PAC) – Quando um servidor que utiliza o protocolo Kerberos para autenticação precisa de verificar o PAC num bilhete de serviço, envia o PAC através do canal de segurança para o seu controlador de domínio para verificação.

### <a name="local-security-authority"></a>Autenticação de Segurança Local

A Autoridade de Segurança Local (LSA) é um subsistema protegido que mantém informações sobre todos os aspetos da segurança local num sistema. Coletivamente conhecida como política de segurança local, a LSA presta vários serviços de tradução entre nomes e identificadores.

O subsistema de segurança LSA fornece serviços tanto no modo kernel como no modo de utilizador para validar o acesso a objetos, verificar privilégios do utilizador e gerar mensagens de auditoria. A LSA é responsável por verificar a validade de todos os bilhetes de sessão apresentados por serviços em domínios fidedignos ou não confiáveis.

### <a name="management-tools"></a>Ferramentas de gestão

Os administradores podem usar *Domínios e Fundos de Diretório Ativo,* *Netdom* e *Nltest* para expor, criar, remover ou modificar fundos.

* *Ative Directory Domains and Trusts* é a Consola de Gestão da Microsoft (MMC) que é usada para administrar fundos de domínio, níveis funcionais de domínio e floresta, e sufixos de nome principal do utilizador.
* As ferramentas de linha de comando *Netdom* e *Nltest* podem ser usadas para encontrar, exibir, criar e gerir fidedignidades. Estas ferramentas comunicam diretamente com a autoridade lSA num controlador de domínio.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as florestas de recursos, veja como funcionam os [fundos florestais em Azure AD DS?][concepts-trust]

Para começar com a criação de um domínio gerido azure AD DS com uma floresta de recursos, consulte [Criar e configurar um domínio gerido por Azure AD DS][tutorial-create-advanced]. Em seguida, pode [criar uma confiança florestal de saída para um domínio no local (pré-visualização)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
