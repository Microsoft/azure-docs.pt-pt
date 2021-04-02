---
title: Como as fidedignidades funcionam para os Serviços de Domínio AZure AD | Microsoft Docs
description: Saiba mais sobre como a confiança da floresta funciona com os Serviços de Domínio AZure AD
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 5c72ab7d085de558ee95f3c602ccc6be6160b322
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620210"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Como as relações de confiança funcionam para as florestas de recursos em Azure Ative Directory Domain Services

Os Serviços de Domínio do Diretório Ativo (DS AD) proporcionam segurança em vários domínios ou florestas através de relações de domínio e confiança florestal. Antes de a autenticação poder ocorrer através de fidedignidades, o Windows tem primeiro de verificar se o domínio solicitado por um utilizador, computador ou serviço tem uma relação de confiança com o domínio da conta de pedido.

Para verificar esta relação de confiança, o sistema de segurança do Windows calcula uma via de confiança entre o controlador de domínio (DC) para o servidor que recebe o pedido e um DC no domínio da conta de pedido.

Os mecanismos de controlo de acesso fornecidos pela AD DS e pelo modelo de segurança distribuído pelo Windows proporcionam um ambiente para o funcionamento de fundos de domínio e floresta. Para que estes fidedignos funcionem corretamente, todos os recursos ou computadores devem ter um caminho de confiança direto para um DC no domínio em que está localizado.

O caminho de confiança é implementado pelo serviço Net Logon utilizando uma ligação autenticada de chamada de procedimento remoto (RPC) à autoridade de domínio fidedigna. Um canal seguro também se estende a outros domínios AD DS através de relações de confiança interdomínio. Este canal seguro é utilizado para obter e verificar informações de segurança, incluindo identificadores de segurança (SIDs) para utilizadores e grupos.

Para uma visão geral de como os trusts se aplicam ao Azure AD DS, consulte [conceitos e funcionalidades da floresta de recursos.][create-forest-trust]

Para começar a usar fundos em Azure AD DS, [crie um domínio gerido que utilize fundos florestais.][tutorial-create-advanced]

## <a name="trust-relationship-flows"></a>Fluxos de relacionamento de confiança

O fluxo de comunicações seguras sobre os fundos determina a elasticidade de um fundo. A forma como cria ou configura uma confiança determina até que ponto a comunicação se estende dentro ou através das florestas.

O fluxo de comunicação sobre os fidedignos é determinado pela direção da confiança. Os fundos podem ser unidirecionais ou de duas vias, e podem ser transitórios ou não transitivos.

O diagrama seguinte mostra que todos os domínios da *Árvore 1* e *da Árvore 2* têm relações de confiança transitivas por defeito. Como resultado, os utilizadores na *Árvore 1* podem aceder a recursos em domínios na *Árvore 2* e os utilizadores na *Árvore 1* podem aceder aos recursos na *Árvore 2,* quando as permissões adequadas são atribuídas no recurso.

![Diagrama de relações de confiança entre duas florestas](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Confianças unidirecionais e bidirecionais

As relações de confiança permitem o acesso aos recursos pode ser de sentido único ou de duas vias.

Um fundo unidirecional é um caminho de autenticação unidirecional criado entre dois domínios. Numa confiança unidirecionais entre *o Domínio A* e o Domínio *B,* os utilizadores do *Domínio A* podem aceder aos recursos no *Domínio B*. No entanto, os utilizadores *no Domínio B* não podem aceder a recursos no Domínio *A*.

Alguns fidedignos unidirecionais podem ser não transitórios ou transitórios dependendo do tipo de confiança que está a ser criada.

Num fundo bidirecionais, *o Domínio A* confia no Domínio *B* e o *Domínio B* confia no Domínio *A*. Esta configuração significa que os pedidos de autenticação podem ser transmitidos entre os dois domínios em ambas as direções. Algumas relações bidireccionals podem ser não transitivas ou transitivas dependendo do tipo de confiança que está a ser criada.

Todos os fundos de domínio numa floresta DS AD são fundos de duas vias e transitivos. Quando um novo domínio infantil é criado, uma confiança transitiva bidireccioniva é automaticamente criada entre o novo domínio infantil e o domínio dos pais.

### <a name="transitive-and-non-transitive-trusts"></a>Fundos transitórios e não transitórios

A transitividade determina se uma confiança pode ser estendida fora dos dois domínios com os quais foi formada.

* Uma confiança transitiva pode ser usada para estender relações de confiança com outros domínios.
* Uma confiança não transitiva pode ser usada para negar relações de confiança com outros domínios.

Cada vez que se cria um novo domínio numa floresta, uma relação de confiança transitiva e bidireccioniva é automaticamente criada entre o novo domínio e o seu domínio principal. Se os domínios infantis forem adicionados ao novo domínio, o caminho da confiança flui para cima através da hierarquia do domínio, estendendo o caminho de confiança inicial criado entre o novo domínio e o seu domínio principal. As relações de confiança transitivas fluem para cima através de uma árvore de domínio à medida que é formada, criando fidedignidades transitivas entre todos os domínios na árvore do domínio.

Os pedidos de autenticação seguem estes caminhos de confiança, para que as contas de qualquer domínio da floresta possam ser autenticadas por qualquer outro domínio na floresta. Com um único sinal em processo, as contas com as permissões adequadas podem aceder a recursos em qualquer domínio da floresta.

## <a name="forest-trusts"></a>Confianças de floresta

Os fundos florestais ajudam-no a gerir uma infraestrutura de DS AD segmentada e a apoiar o acesso a recursos e outros objetos através de múltiplas florestas. Os fundos florestais são úteis para prestadores de serviços, empresas sujeitas a fusões ou aquisições, extranets de negócios colaborativos e empresas que procuram uma solução para a autonomia administrativa.

Usando fundos florestais, você pode ligar duas florestas diferentes para formar uma relação de confiança transitiva de sentido único ou bidireccioniva. Um fundo florestal permite que os administradores conectem duas florestas AD DS com uma única relação de confiança para fornecer uma experiência de autenticação e autorização perfeita em todas as florestas.

Uma confiança florestal só pode ser criada entre um domínio de raiz florestal numa floresta e um domínio de raiz florestal em outra floresta. Os fundos florestais só podem ser criados entre duas florestas e não podem ser implicitamente estendidos a uma terceira floresta. Este comportamento significa que se uma confiança florestal for criada entre *a Floresta 1* e *a Floresta 2,* e outra confiança florestal for criada entre *a Floresta 2* e *a Floresta 3,* *a Floresta 1* não tem uma confiança implícita com *a Floresta 3.*

O diagrama seguinte mostra duas relações separadas de confiança florestal entre três florestas DS AD numa única organização.

![Diagrama de floresta confia relações dentro de uma única organização](./media/concepts-forest-trust/forest-trusts-diagram.png)

Esta configuração de exemplo fornece o seguinte acesso:

* Os utilizadores da *Floresta 2* podem aceder a recursos em qualquer domínio na *Floresta 1* ou *Floresta 3*
* Os utilizadores da *Floresta 3* podem aceder a recursos em qualquer domínio na *Floresta 2*
* Os utilizadores da *Floresta 1* podem aceder a recursos em qualquer domínio na *Floresta 2*

Esta configuração não permite que os utilizadores na *Floresta 1* acedam a recursos na *Floresta 3* ou vice-versa. Para permitir que os utilizadores da *Floresta 1* e *da Floresta 3* partilhem recursos, deve ser criada uma confiança transitiva bidireccioniva entre as duas florestas.

Se for criada uma confiança florestal unidirecionais entre duas florestas, os membros da floresta de confiança podem utilizar recursos localizados na floresta de confiança. No entanto, a confiança opera apenas numa direção.

Por exemplo, quando se cria uma confiança florestal unidirecional entre a *Floresta 1* (a floresta de confiança) e a *Floresta 2* (a floresta de confiança):

* Os membros da *Floresta 1* podem aceder aos recursos localizados na *Floresta 2*.
* Os membros da *Floresta 2* não conseguem aceder aos recursos localizados na *Floresta 1* usando a mesma confiança.

> [!IMPORTANT]
> A floresta de recursos de recursos Azure AD Domain Services apenas suporta uma confiança florestal unidireccionária para o Ative Directory no local.

### <a name="forest-trust-requirements"></a>Requisitos de confiança florestal

Antes de criar uma confiança florestal, tem de verificar se tem a infraestrutura correta do Sistema de Nome de Domínio (DNS) no local. Os fundos florestais só podem ser criados quando uma das seguintes configurações de DNS estiver disponível:

* Um servidor DNS de raiz único é o servidor DE DNS raiz para ambos os espaços de nome DNS florestais - a zona raiz contém delegações para cada um dos espaços de nomes DNS e as dicas de raiz de todos os servidores DNS incluem o servidor DE DNS raiz.
* Quando não existe um servidor DNS de raiz partilhada e os servidores DNS de raiz em cada espaço de nome DNS da floresta usam os reencaminhadores de ADN para cada espaço de nome DNS para encaminhar consultas para nomes no outro espaço de nome.

    > [!IMPORTANT]
    > A floresta de recursos Azure AD Domain Services deve utilizar esta configuração DNS. Hospedar um espaço de nome DNS que não seja o espaço de nome DNS da floresta de recursos não é uma característica dos Serviços de Domínio AD Azure. Os reencaminhadores condicionalistas são a configuração adequada.

* Quando não existe um servidor DNS de raiz partilhada e os servidores DNS de raiz em cada espaço de nome DNS da floresta são utilizados as zonas secundárias DNS são configuradas em cada espaço de nome DNS para encaminhar consultas para nomes no outro espaço de nome.

Para criar uma confiança florestal, você deve ser um membro do grupo Dedmins de Domínio (no domínio das raízes da floresta) ou do grupo Enterprise Admins em Ative Directory. A cada fundo é atribuída uma senha que os administradores de ambas as florestas devem saber. Os membros da Enterprise Admins em ambas as florestas podem criar os fundos em ambas as florestas ao mesmo tempo e, neste cenário, uma palavra-passe que é criptograficamente aleatória é gerada e escrita automaticamente para ambas as florestas.

A confiança florestal de saída para os Serviços de Domínio Azure AD é criada no portal Azure. Não se cria manualmente a confiança com o domínio gerido em si. O fundo florestal que entra deve ser configurado por um utilizador com os privilégios anteriormente indicados no Diretório Ativo no local.

## <a name="trust-processes-and-interactions"></a>Processos e interações de confiança

Muitas transações inter-domínio e inter-florestais dependem de fundos de domínio ou floresta para completar várias tarefas. Esta secção descreve os processos e interações que ocorrem à medida que os recursos são acedidos através de fidedignidades e são avaliadas referências de autenticação.

### <a name="overview-of-authentication-referral-processing"></a>Visão geral do processamento de referenciação de autenticação

Quando um pedido de autenticação é referido a um domínio, o controlador de domínio nesse domínio deve determinar se existe uma relação de confiança com o domínio de onde provém o pedido. A direção do fundo e se a confiança é transitiva ou não transitiva também deve ser determinada antes de autenticar o utilizador para aceder aos recursos no domínio. O processo de autenticação que ocorre entre domínios fidedignos varia de acordo com o protocolo de autenticação em uso. Os protocolos Kerberos V5 e NTLM processam referências para autenticação a um domínio de forma diferente

### <a name="kerberos-v5-referral-processing"></a>Processamento de referência Kerberos V5

O protocolo de autenticação Kerberos V5 depende do serviço Net Logon em controladores de domínio para informações de autenticação e autorização do cliente. O protocolo Kerberos liga-se a um Centro de Distribuição de Chaves online (KDC) e à loja de conta Ative Directory para bilhetes de sessão.

O protocolo Kerberos também utiliza fundos fiduciários para serviços de concessão de bilhetes de transal com bilhetes (TGS) e para validar certificados de atributos de privilégio (PACs) através de um canal seguro. O protocolo Kerberos executa a autenticação cross-realm apenas com o sistema operativo kerberos da marca Windows, como um reino MIT Kerberos e não precisa de interagir com o serviço Net Logon.

Se o cliente utilizar Kerberos V5 para autenticação, solicita um bilhete para o servidor no domínio alvo a partir de um controlador de domínio no seu domínio de conta. O Kerberos KDC atua como um intermediário de confiança entre o cliente e o servidor e fornece uma chave de sessão que permite às duas partes autenticar-se mutuamente. Se o domínio alvo for diferente do domínio atual, o KDC segue um processo lógico para determinar se um pedido de autenticação pode ser remetido:

1. O domínio atual é fidedigno diretamente pelo domínio do servidor que está a ser solicitado?
    * Se sim, envie ao cliente uma referência para o domínio solicitado.
    * Se não, vá para o próximo passo.

2. Existe uma relação de confiança transitiva entre o domínio atual e o próximo domínio no caminho da confiança?
    * Se sim, envie ao cliente uma referência para o próximo domínio no caminho da confiança.
    * Se não, envie ao cliente uma mensagem negada.

### <a name="ntlm-referral-processing"></a>Processamento de referência NTLM

O protocolo de autenticação NTLM depende do serviço Net Logon em controladores de domínio para informações de autenticação e autorização do cliente. Este protocolo autentica clientes que não utilizem a autenticação Kerberos. A NTLM utiliza fundos fiduciários para passar pedidos de autenticação entre domínios.

Se o cliente utilizar o NTLM para autenticação, o pedido inicial de autenticação vai diretamente do cliente para o servidor de recursos no domínio alvo. Este servidor cria um desafio ao qual o cliente responde. Em seguida, o servidor envia a resposta do utilizador a um controlador de domínio no seu domínio de conta de computador. Este controlador de domínio verifica a conta do utilizador na base de dados das suas contas de segurança.

Se a conta não existir na base de dados, o controlador de domínio determina se deve efetuar a autenticação pass-through, encaminhar o pedido ou negar o pedido utilizando a seguinte lógica:

1. O domínio atual tem uma relação de confiança direta com o domínio do utilizador?
    * Se sim, o controlador de domínio envia as credenciais do cliente para um controlador de domínio no domínio do utilizador para a autenticação pass-through.
    * Se não, vá para o próximo passo.

2. O domínio atual tem uma relação de confiança transitiva com o domínio do utilizador?
    * Se sim, passe o pedido de autenticação para o próximo domínio no caminho da confiança. Este controlador de domínio repete o processo verificando as credenciais do utilizador na base de dados das suas próprias contas de segurança.
    * Se não, envie ao cliente uma mensagem negada ao logo.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Processamento baseado em Kerberos de pedidos de autenticação sobre fundos florestais

Quando duas florestas estão ligadas por um fundo florestal, os pedidos de autenticação feitos com os protocolos Kerberos V5 ou NTLM podem ser encaminhados entre florestas para fornecer acesso a recursos em ambas as florestas.

Quando um fundo florestal é estabelecido pela primeira vez, cada floresta recolhe todos os espaços de nome fidedignos na sua floresta parceira e armazena a informação num [objeto de domínio fidedigno.](#trusted-domain-object) Os espaços de nome fidedignos incluem nomes de árvores de domínio, sufixos de nome principal do utilizador (UPN), sufixos de nome principal de serviço (SPN) e espaços de identificação de segurança (SID) usados na outra floresta. Os objetos TDO são replicados no catálogo global.

Antes de os protocolos de autenticação poderem seguir o caminho da confiança florestal, o nome principal de serviço (SPN) do computador de recurso deve ser resolvido para um local na outra floresta. Um SPN pode ser um dos seguintes nomes:

* O nome DNS de um hospedeiro.
* O nome DNS de um domínio.
* O nome distinto de um objeto de ponto de ligação de serviço.

Quando uma estação de trabalho numa floresta tenta aceder a dados num computador de recursos noutra floresta, o processo de autenticação Kerberos contacta o controlador de domínio para obter um bilhete de serviço para a SPN do computador de recurso. Uma vez que o controlador de domínio questiona o catálogo global e determina que o SPN não está na mesma floresta que o controlador de domínio, o controlador de domínio envia uma referência para o seu domínio principal de volta à estação de trabalho. Nessa altura, a estação de trabalho consulta o domínio principal do bilhete de serviço e continua a seguir a cadeia de referência até chegar ao domínio onde o recurso está localizado.

O diagrama e os passos seguintes fornecem uma descrição detalhada do processo de autenticação Kerberos que é usado quando os computadores que executam o Windows tentam aceder a recursos a partir de um computador localizado em outra floresta.

![Diagrama do processo Kerberos sobre um fundo florestal](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. *O utilizador1* assina no *Workstation1* utilizando credenciais do domínio *europe.tailspintoys.com.* O utilizador tenta então aceder a um recurso partilhado no *FileServer1* localizado na floresta *usa.wingtiptoys.com.*

2. *Workstation1* contacta o Kerberos KDC num controlador de domínio no seu domínio, *ChildDC1,* e solicita um bilhete de serviço para o *FileServer1* SPN.

3. *A ChildDC1* não encontra a SPN na sua base de dados de domínios e consulta o catálogo global para ver se algum domínio na floresta *tailspintoys.com* contém este SPN. Como um catálogo global se limita à sua própria floresta, a SPN não é encontrada.

    O catálogo global verifica então a sua base de dados para obter informações sobre quaisquer fundos florestais que sejam estabelecidos com a sua floresta. Se for encontrado, compara os sufixos de nome listados no objeto de domínio fidedigno da floresta (TDO) com o sufixo do SPN alvo para encontrar uma correspondência. Uma vez que uma correspondência é encontrada, o catálogo global fornece uma dica de encaminhamento de volta para *ChildDC1*.

    As dicas de encaminhamento ajudam a direcionar os pedidos de autenticação para a floresta de destino. As dicas só são usadas quando todos os canais de autenticação tradicionais, como o controlador de domínio local e, em seguida, o catálogo global, não conseguem localizar um SPN.

4. *A ChildDC1* envia uma referência para o seu domínio-mãe de volta à *Workstation1*.

5. *A Workstation1* contacta um controlador de domínio em *ForestRootDC1* (seu domínio-mãe) para uma referência a um controlador de domínio *(ForestRootDC2*) no domínio das raízes florestais da floresta *wingtiptoys.com* floresta.

6. *Workstation1* contacta *ForestRootDC2* na floresta *wingtiptoys.com* para um bilhete de serviço para o serviço solicitado.

7. *A ForestRootDC2* contacta o seu catálogo global para encontrar o SPN, e o catálogo global encontra uma correspondência para o SPN e envia-o de volta para *ForestRootDC2*.

8. *ForestRootDC2* envia então o encaminhamento para *usa.wingtiptoys.com* de volta para *a Workstation1*.

9. *Workstation1* contacta o KDC no *ChildDC2* e negoceia o bilhete para o *Utilizador1* ter acesso ao *FileServer1*.

10. Uma vez que *o Workstation1* tem um bilhete de serviço, envia o bilhete de serviço para *FileServer1,* que lê as credenciais de segurança do *Utilizador1* e constrói um token de acesso em conformidade.

## <a name="trusted-domain-object"></a>Objeto de domínio fidedigno

Cada domínio ou confiança florestal dentro de uma organização é representado por um Objeto de Domínio Fidedigno (TDO) armazenado no recipiente *do Sistema* dentro do seu domínio.

### <a name="tdo-contents"></a>Conteúdo de TDO

A informação contida num TDO varia consoante um TDO tenha sido criado por um fundo de domínio ou por um fundo florestal.

Quando um fundo de domínio é criado, atributos como o nome de domínio DNS, o domínio SID, o tipo de confiança, a transitividade de confiança e o nome de domínio recíproco estão representados no TDO. Os TDOs de confiança florestal armazenam atributos adicionais para identificar todos os espaços de nome fidedignos da floresta parceira. Estes atributos incluem nomes de árvores de domínio, sufixos de nome principal do utilizador (UPN), sufixos de nome principal de serviço (SPN) e espaços de identificação de segurança (SID).

Como os fundos são armazenados no Ative Directory como TDOs, todos os domínios de uma floresta têm conhecimento das relações de confiança que estão em vigor em toda a floresta. Da mesma forma, quando duas ou mais florestas são unidas através de fundos florestais, os domínios das raízes florestais em cada floresta têm conhecimento das relações de confiança que estão em vigor em todos os domínios em florestas de confiança.

### <a name="tdo-password-changes"></a>Alterações na palavra-passe do TDO

Ambos os domínios de uma relação de confiança partilham uma palavra-passe, que é armazenada no objeto TDO no Ative Directory. Como parte do processo de manutenção da conta, a cada 30 dias o controlador de domínio fidedignos altera a palavra-passe armazenada no TDO. Porque todos os fundos bidirecionais são, na verdade, dois fidedignos unidirecionais que vão em direções opostas, o processo ocorre duas vezes para fidedignidades bidirecionais.

Um fundo tem um lado de confiança e confiança. No lado de confiança, qualquer controlador de domínio pode ser utilizado para o processo. Do lado da confiança, o emulador PDC executa a alteração da palavra-passe.

Para alterar uma palavra-passe, os controladores de domínio completam o seguinte processo:

1. O emulador principal do controlador de domínio (PDC) no domínio de confiança cria uma nova palavra-passe. Um controlador de domínio no domínio fidedigno nunca inicia a alteração da palavra-passe. É sempre iniciado pelo emulador de pDC de domínio de confiança.

2. O emulador PDC no domínio de confiança define o campo *OldPassword* do objeto TDO para o atual campo *NewPassword.*

3. O emulador PDC no domínio de confiança define o campo *NewPassword* do objeto TDO para a nova palavra-passe. Manter uma cópia da palavra-passe anterior permite reverter para a senha antiga se o controlador de domínio no domínio fidedigno não receber a alteração, ou se a alteração não for replicada antes de ser feito um pedido que utilize a nova senha de confiança.

4. O emulador PDC no domínio de confiança faz uma chamada remota para um controlador de domínio no domínio fidedigno, pedindo-lhe que desembolse a palavra-passe na conta fiduciante para a nova palavra-passe.

5. O controlador de domínio no domínio fidedigno altera a palavra-passe de confiança para a nova palavra-passe.

6. Em cada lado do fundo, as atualizações são replicadas para os outros controladores de domínio no domínio. No domínio de confiança, a mudança desencadeia uma replicação urgente do objeto de domínio fidedigno.

A palavra-passe é agora alterada em ambos os controladores de domínio. A replicação normal distribui os objetos TDO pelos outros controladores de domínio no domínio. No entanto, é possível que o controlador de domínio no domínio de confiança altere a palavra-passe sem atualizar com sucesso um controlador de domínio no domínio fidedigno. Este cenário pode ocorrer porque um canal seguro, que é necessário para processar a mudança de senha, não foi possível estabelecer. Também é possível que o controlador de domínio no domínio fidedigno possa estar indisponível em algum momento durante o processo e pode não receber a senha atualizada.

Para lidar com situações em que a alteração da palavra-passe não é comunicada com sucesso, o controlador de domínio no domínio de confiança nunca altera a nova palavra-passe a menos que tenha autenticado com sucesso (configurar um canal seguro) utilizando a nova palavra-passe. Este comportamento é o motivo pelo qual tanto as palavras-passe antigas como as novas são mantidas no objeto TDO do domínio de confiança.

Uma alteração de palavra-passe não é finalizada até que a autenticação usando a palavra-passe seja bem sucedida. A senha antiga e armazenada pode ser utilizada sobre o canal seguro até que o controlador de domínio no domínio fidedigno receba a nova palavra-passe, permitindo assim um serviço ininterrupto.

Se a autenticação utilizando a nova palavra-passe falhar porque a palavra-passe é inválida, o controlador de domínio fidedigno tenta autenticar usando a senha antiga. Se autenticar com sucesso a senha antiga, retoma o processo de alteração da palavra-passe dentro de 15 minutos.

As atualizações de palavras-passe de confiança precisam de ser replicadas aos controladores de domínio de ambos os lados do fundo dentro de 30 dias. Se a palavra-passe de confiança for alterada após 30 dias e um controlador de domínio tiver apenas a palavra-passe N-2, não pode utilizar a confiança do lado de confiança e não pode criar um canal seguro no lado fidedigno.

## <a name="network-ports-used-by-trusts"></a>Portas de rede utilizadas por fidedignidades

Como os fundos devem ser implantados através de vários limites de rede, podem ter de abranger uma ou mais firewalls. Quando for esse o caso, pode fazer um túnel de tráfego através de uma firewall ou abrir portas específicas na firewall para permitir a passagem do tráfego.

> [!IMPORTANT]
> Os Serviços de Domínio do Diretório Ativo não suportam a restrição do tráfego de RPC do Diretório Ativo a portas específicas.

Leia a secção **de versões windows Server 2008 e versões posteriores** do Artigo de Suporte da Microsoft [Como configurar uma firewall para domínios de Diretório Ativo e fidedignidades](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) para aprender sobre as portas necessárias para uma confiança florestal.

## <a name="supporting-services-and-tools"></a>Serviços e ferramentas de apoio

Para suportar fidedignidades e autenticação, são utilizadas algumas funcionalidades adicionais e ferramentas de gestão.

### <a name="net-logon"></a>Net Logon

O serviço Net Logon mantém um canal seguro de um computador baseado no Windows para um DC. Também é usado nos seguintes processos relacionados com a confiança:

* Configuração e gestão de confiança - Net Logon ajuda a manter senhas de confiança, recolhe informações de confiança e verifica confiança interagindo com o processo LSA e o TDO.

    Para os fundos florestais, a informação de confiança inclui o registo Forest Trust Information *(FTInfo),* que inclui o conjunto de espaços de nome que uma floresta de confiança afirma gerir, anotado com um campo que indica se cada reivindicação é confiável pela floresta de confiança.

* Autenticação – Fornece credenciais de utilizador sobre um canal seguro a um controlador de domínio e devolve os SIDs de domínio e os direitos de utilizador para o utilizador.

* Localização do controlador de domínio – Ajuda a encontrar ou localizar controladores de domínio num domínio ou em domínios.

* Validação pass-through – Credenciais de utilizadores em outros domínios são processadas pela Net Logon. Quando um domínio de confiança precisa de verificar a identidade de um utilizador, transmite as credenciais do utilizador através do Net Logon para o domínio fidedigno para verificação.

* Verificação do Certificado de Atributo de Privilégio (PAC) – Quando um servidor que utiliza o protocolo Kerberos para autenticação precisa de verificar o PAC num bilhete de serviço, envia o PAC através do canal seguro para o seu controlador de domínio para verificação.

### <a name="local-security-authority"></a>Autenticação de Segurança Local

A Autoridade de Segurança Local (LSA) é um subsistema protegido que mantém informações sobre todos os aspetos da segurança local num sistema. Colectivamente conhecida como política de segurança local, a LSA fornece vários serviços de tradução entre nomes e identificadores.

O subsistema de segurança LSA fornece serviços tanto no modo kernel como no modo de utilizador para validar o acesso a objetos, verificar privilégios do utilizador e gerar mensagens de auditoria. A LSA é responsável por verificar a validade de todos os bilhetes de sessão apresentados por serviços em domínios fidedignos ou não confiáveis.

### <a name="management-tools"></a>Ferramentas de gestão

Os administradores podem usar *Domínios e Fidedignidades de Diretório Ativo,* *Netdom* e *Nltest* para expor, criar, remover ou modificar fidedignidades.

* *Ative Directory Domains and Trusts* é a Consola de Gestão da Microsoft (MMC) que é usada para administrar fundos de domínio, níveis funcionais de domínio e floresta, e sufixos de nome principal do utilizador.
* As ferramentas de linha de comando *Netdom* e *Nltest* podem ser usadas para encontrar, exibir, criar e gerir fidedignos. Estas ferramentas comunicam diretamente com a autoridade LSA num controlador de domínio.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as florestas de recursos, veja [como funcionam os fundos florestais em Azure AD DS?][concepts-trust]

Para começar com a criação de um domínio gerido com uma floresta de recursos, consulte [Criar e configurar um domínio gerido Azure AD DS][tutorial-create-advanced]. Em seguida, pode [criar uma confiança florestal de saída para um domínio no local.][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
