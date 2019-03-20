---
title: Guia de segurança de armazenamento do Azure | Documentos da Microsoft
description: Detalhes dos muitos métodos de proteger o armazenamento do Azure, incluindo mas não limitado a RBAC, o Storage Service Encryption, o encriptação do lado do cliente, o SMB 3.0 e o Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 56c09d9c6d1249713de7c6a0428ad2a124eee157
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013070"
---
# <a name="azure-storage-security-guide"></a>Guia de segurança de armazenamento do Azure

Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que juntos permitem aos desenvolvedores criar aplicativos seguros:

- Todos os dados escritos no armazenamento do Azure são automaticamente encriptados usando [Storage Service Encryption (SSE)](storage-service-encryption.md). Para obter mais informações, consulte [anunciando encriptação predefinido para os Blobs do Azure, ficheiros, tabelas e armazenamento de filas](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) e o controlo de acesso baseado em funções (RBAC) são suportadas para o armazenamento do Azure para operações de gestão de recursos e operações de dados, da seguinte forma:   
    - Pode atribuir funções RBAC de âmbito para a conta de armazenamento para entidades de segurança e utilize o Azure AD para autorizar as operações de gestão de recursos, tais como a gestão de chaves.
    - Integração do Azure AD é suportada em pré-visualização para operações de dados nos serviços de BLOBs e filas. Pode atribuir funções RBAC no âmbito de uma subscrição, grupo de recursos, conta de armazenamento, ou um contentor individual ou fila para um principal de segurança ou uma identidade gerida para recursos do Azure. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory (pré-visualização)](storage-auth-aad.md).   
- Dados podem ser protegidos em trânsito entre um aplicativo e o Azure, utilizando [encriptação do lado do cliente](../storage-client-side-encryption.md), HTTPS ou SMB 3.0.  
- Os discos de SO e os dados utilizados por máquinas virtuais do Azure podem ser encriptados utilizando [do Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Acesso delegado para os objetos de dados no armazenamento do Azure pode ser concedido usando [assinaturas de acesso partilhado](../storage-dotnet-shared-access-signature-part-1.md).

Este artigo fornece uma visão geral de cada um desses recursos de segurança que podem ser utilizados com o armazenamento do Azure. Ligações são fornecidas para artigos que vão dar detalhes de cada funcionalidade, pode facilmente fazer ainda mais investigação em cada tópico.

Aqui estão os tópicos para serem abordadas neste artigo:

* [Segurança de plano de gestão](#management-plane-security) – proteger a sua conta de armazenamento

  O plano de gestão consiste nos recursos utilizados para gerir a sua conta de armazenamento. Esta secção abrange o modelo de implementação Azure Resource Manager e como utilizar o controlo de acesso baseado em funções (RBAC) para controlar o acesso às contas de armazenamento. Também aborda a gerir as chaves de conta de armazenamento e como regenerá-los.
* [Segurança do plano de dados](#data-plane-security) – proteger o acesso aos seus dados

  Nesta secção, vamos permitir o acesso aos objetos de dados reais na sua conta de armazenamento, como blobs, ficheiros, filas e tabelas, com assinaturas de acesso partilhado e políticas de acesso armazenado. Abordaremos SAS de nível de serviço e ao nível da conta SAS. Também veremos como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo utilizado para HTTPS e como revogar uma assinatura de acesso partilhado sem aguardar expire.
* [Encriptação em Trânsito](#encryption-in-transit)

  Esta secção descreve como proteger os dados quando a transferência dentro ou fora do armazenamento do Azure. Vamos falar sobre a utilização recomendada de HTTPS e a encriptação utilizado pelo SMB 3.0 para partilhas de ficheiros do Azure. Também podemos irá dar uma olhada na encriptação do lado do cliente, que permite-lhe encriptar os dados antes de é transferido para o armazenamento num aplicativo cliente e para descriptografar os dados depois de serem transferido para fora do armazenamento.
* [Encriptação Inativa](#encryption-at-rest)

  Vamos falar sobre o Storage Service Encryption (SSE), que agora está automaticamente ativada para contas de armazenamento de novas e existentes. Vamos também ver como pode utilizar o Azure Disk Encryption e explore as diferenças básicas e casos de encriptação de disco em comparação com o SSE em comparação com a encriptação do lado do cliente. Vamos rapidamente ver a conformidade FIPS para a região E.U.A. Computadores do Governo.
* Usando [a análise de armazenamento](#storage-analytics) auditar o acesso do armazenamento do Azure

  Esta secção descreve como encontrar informações nos registos de análise de armazenamento para um pedido. Vamos dar uma olhada em análise de armazenamento real dados de registo e veja como discernir se uma solicitação é feita com a chave de conta de armazenamento, com uma assinatura de acesso partilhado, ou anonimamente e se foi concluída com êxito ou falha.
* [Ativação baseada no Browser e clientes utilizando a CORS](#Cross-Origin-Resource-Sharing-CORS)

  Esta seção fala sobre como permitir que os recursos de várias origens (CORS) de partilha. Vamos falar sobre o acesso entre domínios e como manipulá-lo com as capacidades CORS incorporadas no armazenamento do Azure.

## <a name="management-plane-security"></a>Segurança de plano de gestão
O plano de gestão consiste em operações que afetam a própria conta de armazenamento. Por exemplo, pode criar ou eliminar uma conta de armazenamento, obter uma lista de contas de armazenamento numa subscrição, obter as chaves de conta de armazenamento ou voltar a gerar as chaves de conta de armazenamento.

Quando cria uma nova conta de armazenamento, selecione um modelo de implementação de clássico ou do Resource Manager. O modelo clássico de criação de recursos no Azure só permite o acesso de tudo ou nada para a subscrição e, por sua vez, a conta de armazenamento.

Este guia concentra-se no modelo do Resource Manager que é o meio recomendado para a criação de contas de armazenamento. Com as contas de armazenamento do Resource Manager, em vez de dando-acesso para a subscrição completa, pode controlar o acesso num nível mais finito para o plano de gestão com o controlo de acesso baseado em funções (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)
Vamos falar sobre o que é o RBAC e como pode usá-lo. Cada subscrição do Azure tem um Azure Active Directory. Os utilizadores, grupos e aplicações a partir desse diretório podem ser concedidas acesso para gerir os recursos na subscrição do Azure que utilizam o modelo de implementação do Resource Manager. Este tipo de segurança é referido como controlo de acesso baseado em funções (RBAC). Para gerir este acesso, pode utilizar o [portal do Azure](https://portal.azure.com/), o [ferramentas de CLI do Azure](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), ou o [APIs de REST do fornecedor de recursos de armazenamento de Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Com o modelo do Resource Manager, coloque a conta de armazenamento num grupo e controlar o acesso recursos para o plano de gestão dessa conta de armazenamento específica com o Azure Active Directory. Por exemplo, pode dar aos utilizadores específicos a capacidade de aceder as chaves de conta de armazenamento, enquanto outros utilizadores podem ver informações sobre a conta de armazenamento, mas não é possível aceder as chaves de conta de armazenamento.

#### <a name="granting-access"></a>Conceder acesso
O acesso é concedido ao atribuir a função RBAC adequada para os utilizadores, grupos e aplicações, no âmbito da direita. Para conceder acesso para a subscrição completa, atribuir uma função ao nível da subscrição. Pode conceder acesso a todos os recursos num grupo de recursos ao conceder permissões para o grupo de recursos. Também pode atribuir funções específicas a recursos específicos, como contas de armazenamento.

Aqui estão os pontos principais que precisa saber sobre como utilizar o RBAC para acessar as operações de gestão de uma conta de armazenamento do Azure:

* Quando atribui acesso, basicamente, atribuir uma função para a conta que pretende ter acesso. Pode controlar o acesso para as operações utilizadas para gerir essa conta de armazenamento, mas não para os objetos de dados na conta. Por exemplo, pode conceder permissão para recuperar as propriedades da conta de armazenamento (por exemplo, redundância), mas não para um contentor ou os dados dentro de um contêiner no interior do armazenamento de Blobs.
* Para que alguém tem permissão para aceder os objetos de dados na conta de armazenamento, pode dar-lhes permissão para ler as chaves de conta de armazenamento e que o utilizador, em seguida, pode utilizar essas chaves para aceder a blobs, filas, tabelas e ficheiros.
* Funções podem ser atribuídas para uma conta de utilizador específico, um grupo de utilizadores ou para uma aplicação específica.
* Cada função possui uma lista de ações e não ações. Por exemplo, a função de contribuinte de Máquina Virtual tem uma ação de "listKeys" que permite que as chaves de conta de armazenamento a ser lido. O colaborador tem "Não ações", como atualizar o acesso dos utilizadores no Active Directory.
* Funções de armazenamento incluem (mas não se limitam a) as seguintes funções:

  * Proprietário – podem gerir tudo, incluindo o acesso.
  * Contribuinte – elas podem fazer nada o proprietário pode exceto atribuir acesso. Alguém com esta função pode ver e voltar a gerar as chaves de conta de armazenamento. Com as chaves de conta de armazenamento, eles podem acessar os objetos de dados.
  * Leitor – podem visualizar informações sobre a conta de armazenamento, exceto segredos. Por exemplo, se atribuir uma função com permissões de leitura na conta de armazenamento para alguém, podem visualizar as propriedades da conta de armazenamento, mas não podem efetuar quaisquer alterações às propriedades ou exibir as chaves de conta de armazenamento.
  * Contribuinte de conta de armazenamento – pode gerir a conta de armazenamento – podem ler os grupos de recursos e recursos, a subscrição e criar e gerir implementações de grupo de recursos de subscrição. Também podem aceder as chaves de conta de armazenamento, que por sua vez, significa que podem aceder ao plano de dados.
  * Administrador de acesso do utilizador – eles podem gerir o acesso de utilizador para a conta de armazenamento. Por exemplo, eles podem conceder acesso de leitor para um utilizador específico.
  * Contribuinte de máquina virtual – podem gerir máquinas virtuais, mas não a conta de armazenamento ao qual estão ligados. Esta função pode listar as chaves de conta de armazenamento, o que significa que o utilizador a quem atribuir esta função pode atualizar o plano de dados.

    Para um utilizador criar uma máquina virtual, têm de ser capaz de criar o ficheiro VHD correspondente numa conta de armazenamento. Para tal, têm de ser capaz de obter a chave de conta de armazenamento e passá-lo para a API de criar a VM. Por conseguinte, têm de ter esta permissão, de modo que podem listar as chaves de conta de armazenamento.
* A capacidade de definir funções personalizadas é uma funcionalidade que permite que Componha um conjunto de ações de uma lista de ações disponíveis que podem ser executadas em recursos do Azure.
* O utilizador tem de ser definido no Azure Active Directory antes de pode atribuir-lhes uma função.
* Pode criar um relatório de quem concedido/revogado que tipo de acesso de/para quem e em quais âmbito com o PowerShell ou a CLI do Azure.

#### <a name="resources"></a>Recursos
* [Controlo de Acesso Baseado em Funções do Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  Este artigo explica o Controlo de Acesso Baseado em Funções do Azure Active Directory e como funciona.
* [RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md)

  Este artigo fornece detalhes sobre todas as funções incorporadas disponíveis no RBAC.
* [Compreender a implementação do Resource Manager e a implementação clássica](../../azure-resource-manager/resource-manager-deployment-model.md)

  Este artigo explica os modelos de implementação clássica e de implementação do Resource Manager e explica as vantagens de utilizar os grupos de recursos e do Resource Manager. Ele explica como a computação do Azure, rede e fornecedores de armazenamento funcionam no modelo do Resource Manager.
* [Gerir o Controlo de Acesso Baseado em Funções com a API REST](../../role-based-access-control/role-assignments-rest.md)

  Este artigo mostra como utilizar a API REST para gerir o RBAC.
* [Referência da API REST do Fornecedor de Recursos de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Esta referência de API descreve as APIs que pode utilizar para gerir a sua conta de armazenamento por meio de programação.
* [Utilize o recurso de API do Gerenciador de autenticação para aceder a subscrições](../../azure-resource-manager/resource-manager-api-authentication.md)

  Este artigo mostra como autenticar com as APIs do Resource Manager.
* [Controlo de Acesso Baseado em Funções para o Microsoft Azure no Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Esta é uma ligação para um vídeo do Channel 9 da conferência Ignite 2015 MS. Nesta sessão, fala-se sobre as capacidades de gestão de acesso e de criação de relatórios no Azure e são exploradas as melhores práticas relativamente à proteção do acesso às subscrições do Azure com o Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Gerir as chaves de conta de armazenamento
Chaves de conta de armazenamento são cadeias de caracteres de 512 bits criadas pelo Azure que, juntamente com o nome da conta de armazenamento, pode ser usado para acessar os objetos de dados armazenados na conta de armazenamento, por exemplo, blob, a entidades dentro de uma tabela, fila de mensagens e ficheiros numa partilha de ficheiros do Azure. Controlar o acesso para o armazenamento conta chaves controla o acesso ao plano de dados para essa conta de armazenamento.

Cada conta de armazenamento tem duas chaves referidas como "Chave 1" e "Chave 2" no [portal do Azure](https://portal.azure.com/) e nos cmdlets do PowerShell. Estes podem ser regeneradas manualmente usando um dos vários métodos, incluindo, mas não limitado a utilizar o [portal do Azure](https://portal.azure.com/), PowerShell, CLI do Azure, ou programaticamente usando a biblioteca de cliente de armazenamento do .NET ou os serviços de armazenamento do Azure API DE REST.

Há inúmeras razões para regenerar as chaves de conta de armazenamento.

* Pode voltar a gerá-los de forma regular, por motivos de segurança.
* Seria regenerar as chaves de conta de armazenamento, se alguém gerido para hack numa aplicação e obter a chave que foi inserido no código ou guardado num ficheiro de configuração, dando-lhes acesso total à sua conta de armazenamento.
* Outro caso para regenerar a chave é se sua equipe está usando um aplicativo de Explorador de armazenamento que retém a chave de conta de armazenamento e um dos membros da Equipe sai. A aplicação iria continuar a trabalhar, conceder acesso à sua conta de armazenamento depois de estarem já era. Isto é, na verdade, a principal razão criaram assinaturas de acesso partilhado ao nível da conta – pode utilizar uma conta de nível SAS em vez de armazenar as chaves de acesso num ficheiro de configuração.

#### <a name="key-regeneration-plan"></a>Plano de regeneração da chave
Não queira apenas regenerar a chave de que está a utilizar sem algum planejamento. Se fizer isso, poderia cortados todo o acesso a essa conta de armazenamento, que pode causar interrupções importantes. É por isso existem duas chaves. Deve voltar a gerar uma chave de cada vez.

Antes de voltar a gerar as chaves, certifique-se de que tem uma lista de todas as suas aplicações dependentes na conta de armazenamento, bem como quaisquer outros serviços que está a utilizar no Azure. Por exemplo, se estiver a utilizar os serviços de multimédia do Azure que dependem de sua conta de armazenamento, tem volte a sincronizar as chaves de acesso com o seu serviço de suporte de dados depois de regenerar a chave. Se estiver a utilizar todas as aplicações, tais como o Explorador de armazenamento, terá de fornecer as chaves novas para esses aplicativos também. Se tiver VMs cujos ficheiros VHD são armazenados na conta de armazenamento, eles não serão afetados pela regenerar as chaves de conta de armazenamento.

Pode voltar a gerar as suas chaves no portal do Azure. Depois das chaves são regeneradas, eles podem demorar até 10 minutos a ser sincronizados em todos os serviços de armazenamento.

Quando estiver pronto, eis o processo geral com detalhes sobre como deve alterar a sua chave. Neste caso, o pressuposto é que estiver a utilizar atualmente chave 1 e pretender alterar tudo o que utilizar em vez disso, a chave 2.

1. Regenere chave 2 para se certificar de que ele é seguro. Pode fazê-lo no portal do Azure.
2. Em todos os aplicativos onde está armazenada a chave de armazenamento, altere a chave de armazenamento a utilizar o novo valor de chave 2. Testar e publicar a aplicação.
3. Afinal de contas de aplicações e serviços estão operacionais e em execução com êxito, a regenerar chave 1. Isto garante que qualquer pessoa a quem não tiver expressamente dado a nova chave já não terá acesso à conta de armazenamento.

Se estiver a utilizar a chave 2, pode usar o mesmo processo, mas inverter os nomes de chaves.

É possível migrar através de dois dias, a alteração de cada aplicação para utilizar a nova chave e publicá-la. Depois de terminar, deve, em seguida, volte atrás e voltar a gerar a chave antiga já não funciona.

Outra opção é colocar a chave de conta de armazenamento num [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) como um segredo e ter seus aplicativos obter a chave a partir daí. Em seguida, quando regenerar a chave e atualizar o Azure Key Vault, os aplicativos não precisarão de ser novamente implementada porque eles selecionará a nova chave do Cofre de chaves do Azure automaticamente. Tenha em atenção que pode ter a aplicação a ler a chave de cada vez que for preciso, ou cache na memória e se falhar quando utilizá-lo, obter a chave novamente de Cofre de chaves do Azure.

Também com o Azure Key Vault adiciona outro nível de segurança para as chaves de armazenamento. Se usar esse método, nunca terá o inseridos no código de chave de armazenamento num arquivo de configuração, o que remove essa Avenida de alguém o acesso às chaves sem permissão específica.

Outra vantagem de utilizar o Azure Key Vault é que também pode controlar o acesso para as suas chaves com o Azure Active Directory. Isso significa que pode conceder acesso a muitos dos aplicativos que precisam para obter as chaves do Azure Key Vault e saber que outras aplicações não conseguir aceder às chaves, sem conceder-lhes permissão especificamente.

> [!NOTE]
> A Microsoft recomenda utilizar apenas uma das chaves em todos os seus aplicativos ao mesmo tempo. Se utilizar a chave 1 em alguns locais e a chave 2 em outros, não será capaz de rodar as chaves sem algum aplicativo perder o acesso.

#### <a name="resources"></a>Recursos

* [Gerir definições de conta de armazenamento no portal do Azure](storage-account-manage.md)
* [Referência da API REST do Fornecedor de Recursos de Armazenamento do Azure](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Segurança de plano de dados
Segurança de plano de dados refere-se para os métodos usados para proteger os objetos de dados armazenados no armazenamento do Azure – a blobs, filas, tabelas e ficheiros. Já vimos métodos para encriptar os dados e a segurança durante o trânsito dos dados, mas como fazer para informações sobre como controlar o acesso aos objetos?

Tem três opções para autorizar o acesso a objetos de dados no armazenamento do Azure, incluindo:

- Utilizar o Azure AD para autorizar o acesso a contentores e filas (pré-visualização). O Azure AD proporciona vantagens em relação a outras abordagens para autorização, incluindo a remover a necessidade de armazenar segredos no seu código. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory (pré-visualização)](storage-auth-aad.md). 
- As chaves de conta de armazenamento a utilizar para autorizar o acesso por meio da chave partilhada. Autorizar através de chave partilhada requer a armazenar as chaves de conta de armazenamento na sua aplicação, para que a Microsoft recomenda a utilização do Azure AD em vez disso, sempre que possível. Para aplicações de produção ou para autorizar o acesso a ficheiros e de tabelas do Azure, continue a utilizar a chave partilhada enquanto a integração do Azure AD está em pré-visualização.
- Utilização de assinaturas de acesso partilhado para conceder permissões controladas para objetos de dados específicos para um período de tempo específico.

Além disso, para armazenamento de BLOBs, pode permitir o acesso público para blobs ao definir o nível de acesso para o contentor que retém os blobs em conformidade. Se definir o acesso para um contentor para o Blob ou contentor, permitirá o acesso de leitura público para blobs existentes nesse contentor. Isso significa que qualquer pessoa com uma URL apontando para um blob nesse contentor pode abrir num navegador sem utilizar uma assinatura de acesso partilhado ou ter as chaves de conta de armazenamento.

Além de limitar o acesso por meio de autorização, também pode utilizar [Firewalls e redes virtuais](storage-network-security.md) para limitar o acesso à conta de armazenamento com base nas regras de rede.  Esta permite que a abordagem negar o acesso ao tráfego da internet pública e conceder acesso apenas a determinados redes virtuais do Azure ou da internet pública intervalos de endereços IP.

### <a name="storage-account-keys"></a>Chaves de Contas de Armazenamento
Chaves de conta de armazenamento são criadas pelo Azure que, juntamente com o nome da conta de armazenamento, pode ser usado para acessar os objetos de dados armazenados na conta de armazenamento de cadeias de 512 bits.

Por exemplo, pode ler blobs, escrever em filas, criar tabelas e modifique os ficheiros. Muitas destas ações podem ser efetuadas através do portal do Azure, ou usando uma das muitas aplicações de Explorador de armazenamento. Também pode escrever código para usar a API REST ou uma das bibliotecas de cliente do armazenamento para executar estas operações.

Como discutido na seção sobre o [segurança de plano de gestão](#management-plane-security), aceder às chaves de armazenamento para uma conta de armazenamento clássico pode ser concedida ao dar acesso total à subscrição do Azure. Acesso às chaves de armazenamento para uma conta de armazenamento com o modelo Azure Resource Manager pode ser controlado por meio do controle de acesso baseado em funções (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como delegar acesso a objetos na sua conta com assinaturas de acesso partilhado e políticas de acesso armazenadas
Uma assinatura de acesso partilhado é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI que lhe permite delegar acesso a objetos de armazenamento e especificar limitações, como as permissões e o intervalo de data/hora de acesso.

Pode conceder acesso a blobs, contentores, mensagens de filas, ficheiros e tabelas. Com tabelas, na verdade, pode conceder permissão para aceder um intervalo de entidades na tabela ao especificar os intervalos de chave partição e de linha para o qual pretende que o usuário tenha acesso. Por exemplo, se tiver dados armazenados com uma chave de partição do Estado geográfica, poderia dar alguém acesso a apenas os dados para da Califórnia.

Noutro exemplo, pode dar um token SAS que lhe permite escrever entradas para uma fila de um aplicativo web e dar uma função de trabalho de aplicação de função um token SAS para receber mensagens da fila e processá-las. Ou poderia dar um cliente um token SAS podem utilizar para carregar imagens para um contentor no armazenamento de BLOBs bem como fornecer uma permissão de aplicação web para ler essas imagens. Em ambos os casos, há uma separação das preocupações – cada aplicativo pode receber apenas o acesso que necessitam para realizar suas tarefas. Isso é possível através da utilização de assinaturas de acesso partilhado.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Por que pretende utilizar assinaturas de acesso partilhado
Por que iria querer utilizar uma SAS em vez de apenas-lo a sua chave de conta de armazenamento, o que é muito mais fácil? -Lo a sua chave de conta de armazenamento é como a partilha as chaves da sua Unido de armazenamento. Ele concede acesso completo. Alguém poderia utilizar as suas chaves e carregar sua biblioteca de música todo para a conta de armazenamento. Eles também poderiam substituir os ficheiros infetados com vírus versões ou roubar os seus dados. Dar acesso ilimitado à sua conta de armazenamento é algo que não deve ser levada a sério.

Com assinaturas de acesso partilhado, pode dar um cliente apenas as permissões necessárias para uma quantidade limitada de tempo. Por exemplo, se alguém está a carregar um blob para a sua conta, pode conceder-lhes acesso de escrita para o tempo carregar o blob (consoante o tamanho do blob, é claro). E se mudar de ideias, pode revogar o acesso.

Além disso, pode especificar que os pedidos efetuados com uma SAS estão limitados a um determinado endereço IP ou intervalo de endereços IP externo para o Azure. Também pode exigir que os pedidos são efetuados utilizando um protocolo específico (HTTPS ou HTTP/HTTPS). Isso significa que se quiser apenas permitir o tráfego HTTPS, que pode definir apenas o protocolo necessário para HTTPS e o tráfego HTTP será bloqueado.

#### <a name="definition-of-a-shared-access-signature"></a>Definição de uma assinatura de acesso partilhado
Uma assinatura de acesso partilhado é um conjunto de parâmetros de consulta anexado ao URL apontando para o recurso

que fornece informações sobre o acesso permitido e o período de tempo para o qual o acesso é permitido. Eis um exemplo; Este URI fornece acesso de leitura para um blob para cinco minutos. Tenha em atenção que os parâmetros de consulta SAS tem de ser codificados de URL, como % 3A para dois pontos (:) ou % 20 para um espaço.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Como a assinatura de acesso partilhado está autorizada pelo serviço de armazenamento do Azure
Quando o serviço de armazenamento recebe o pedido, aceita os parâmetros de consulta de entrada e cria uma assinatura usando o mesmo método como o programa de chamada. Em seguida, compara as duas assinaturas. Se estes concordarem, em seguida, o serviço de armazenamento pode verificar a versão do serviço de armazenamento para certificar-se de que é válido, certifique-se de que a data e hora atuais estão dentro da janela especificada, certifique-se de que o acesso solicitado corresponde ao pedido efetuado, etc.

Por exemplo, com nossos URL acima, se o URL foi a apontar para um ficheiro em vez de um blob, este pedido falha porque Especifica que a assinatura de acesso partilhado é para um blob. Se o comando REST que está a ser chamado atualizar um blob, falhe porque a assinatura de acesso partilhado Especifica que o acesso de leitura apenas é permitido.

#### <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado
* Uma SAS de nível de serviço pode ser utilizada para aceder a recursos específicos numa conta de armazenamento. Alguns exemplos disso são a obter uma lista de blobs num contentor, transferir um blob, atualizar uma entidade numa tabela, adicionar mensagens a uma fila ou carregar um ficheiro para uma partilha de ficheiros.
* Uma SAS de nível de conta pode ser utilizada para aceder a algo que pode servir-se para uma SAS de nível de serviço. Além disso, ele pode permitir que as opções para recursos que não são permitidas com uma SAS de nível de serviço, como a capacidade de criar contentores, tabelas, filas e partilhas de ficheiros. Também pode especificar o acesso a vários serviços, ao mesmo tempo. Por exemplo, poderá dar alguém aceder a blobs e ficheiros na sua conta de armazenamento.

#### <a name="creating-a-sas-uri"></a>Criar um URI de SAS
1. Pode criar um URI a pedido, definindo todos os parâmetros de consulta cada vez.

   Essa abordagem é flexível, mas se tiver um conjunto lógico de parâmetros que são semelhantes a cada vez, através de uma política de acesso armazenadas é uma idéia melhor.
2. Pode criar uma política de acesso armazenadas para um contentor inteiro, a partilha de ficheiros, a tabela ou fila. Em seguida, pode utilizar isso como base para os URIs de SAS que cria. Permissões com base nas políticas de acesso armazenadas podem ser facilmente revogadas. Pode ter até cinco políticas definidas em cada contentor, fila, tabela ou partilha de ficheiros.

   Por exemplo, se fosse fazer muitas pessoas, leia os blobs num contentor específico, poderia criar uma política de acesso armazenadas que diz "dê acesso de leitura" e outras definições que serão os mesmos cada vez. Em seguida, pode criar um URI de SAS, utilizando as definições da política de acesso armazenadas e especificando a data/hora de expiração. A vantagem disso é que não precisa de toda vez especificar todos os parâmetros de consulta.

#### <a name="revocation"></a>Revogação
Suponha que a SAS foi comprometida ou, se pretender alterá-lo devido a requisitos de conformidade a normas ou de segurança corporativa. Como revogar o acesso a um recurso com essa SAS? Depende de como criou o URI de SAS.

Se estiver a utilizar o ad hoc URIs, tem três opções. Pode emitir tokens SAS com políticas de expiração curto e aguarde que a SAS expire. Pode mudar o nome ou eliminar o recurso (supondo que o token foi no âmbito de um único objeto). Pode alterar as chaves de conta de armazenamento. Essa última opção pode ter um impacto significativo, dependendo de quantos serviços utilizar essa conta de armazenamento e provavelmente não é algo que pretende fazer sem algum planejamento.

Se estiver a utilizar uma SAS derivada de uma política de acesso armazenadas, pode remover o acesso ao revogar a política de acesso armazenadas – só pode alterá-lo para que já expirou ou pode removê-lo completamente. Isso entra em vigor imediatamente e invalida todas as SAS criados com essa política de acesso armazenado. A atualizar ou remover a política de acesso armazenadas podem acessar esse contentor específico, a partilha de ficheiros, de pessoas de impacto de tabela ou fila através de SAS, mas se os clientes são escritos para que eles solicitar uma SAS novo quando antigo se torna inválido, isso funcionará bem.

Como com uma SAS derivada de uma política de acesso armazenadas dá-lhe a capacidade de revogar esse SAS imediatamente, é a prática recomendada sempre usar políticas de acesso armazenado sempre que possível.

#### <a name="resources"></a>Recursos
Para obter informações mais detalhadas sobre como utilizar assinaturas de acesso partilhado e armazenados políticas de acesso, completo com exemplos, consulte os artigos seguintes:

* Estes são os artigos de referência.

  * [Serviço SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Este artigo fornece exemplos de como utilizar uma SAS de nível de serviço com arquivos, intervalos de tabela, fila de mensagens e blobs.
  * [Construindo um serviço SAS](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Construir uma SAS de conta](https://msdn.microsoft.com/library/mt584140.aspx)
* Estes são os tutoriais para utilizar a biblioteca de cliente .NET para criar assinaturas de acesso partilhado e políticas de acesso armazenado.

  * [Utilizar assinaturas de acesso partilhado (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Assinaturas de acesso, parte 2 de partilhado: Criar e utilizar um SAS com o serviço Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Este artigo inclui uma explicação sobre o modelo SAS, exemplos de assinaturas de acesso partilhado, e recomendações para a prática recomendada usar de SAS. Também discuti é a revogação de permissão concedida.

* Authentication

  * [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Introdução ao Tutorial de assinaturas de acesso partilhado

  * [Introdução ao Tutorial de SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Encriptação em trânsito
### <a name="transport-level-encryption--using-https"></a>Encriptação de nível de transporte – através de HTTPS
Outra etapa que deve efetuar para garantir a segurança dos seus dados de armazenamento do Azure é encriptar os dados entre o cliente e o armazenamento do Azure. A primeira recomendação é sempre usar o [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protocolo, o que garante uma comunicação segura através da Internet pública.

Para que um canal de comunicação segura, deve sempre usar HTTPS ao chamar as APIs REST ou aceder a objetos no armazenamento. Além disso, **assinaturas de acesso partilhado**, que pode ser utilizado para delegar acesso a objetos de armazenamento do Azure, incluir uma opção para especificar que apenas o protocolo HTTPS pode ser usado quando utilizar assinaturas de acesso partilhado, garantindo que qualquer pessoa a enviar ligações com SAS tokens irão utilizar o protocolo apropriado.

Pode impor a utilização de HTTPS ao chamar as APIs REST para aceder a objetos nas contas de armazenamento, permitindo [transferência segura necessária](../storage-require-secure-transfer.md) para a conta de armazenamento. Ligações através de HTTP irão ser recusadas assim que esta opção estiver ativada.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Usando a criptografia durante o trânsito com partilhas de ficheiros do Azure
[Os ficheiros do Azure](../files/storage-files-introduction.md) suporta a encriptação através de SMB 3.0 e com HTTPS ao usar a API de REST de ficheiros. Ao montar fora da região do Azure, a partilha de ficheiros do Azure está localizado, por exemplo, no local ou noutra região do Azure, o SMB 3.0 com a encriptação é sempre necessário. SMB 2.1 não suporta a encriptação, portanto, por predefinição ligações só são permitidas na mesma região no Azure, mas SMB 3.0 com a criptografia pode ser imposta por [necessidade de transferência segura](../storage-require-secure-transfer.md) para a conta de armazenamento.

SMB 3.0 com a encriptação está disponível no [todos suportados sistemas operativos Windows e Windows Server](../files/storage-how-to-use-files-windows.md) , exceto o Windows 7 e Windows Server 2008 R2, que só suportam SMB 2.1. Também é suportado o SMB 3.0 [macOS](../files/storage-how-to-use-files-mac.md) e, em distribuições de [Linux](../files/storage-how-to-use-files-linux.md) a utilizar o kernel de Linux 4.11 e acima. Suporte a encriptação SMB 3.0 também foi backported para versões mais antigas do kernel do Linux por várias distribuições do Linux, consulte [requisitos do cliente SMB de compreensão](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Utilizar a encriptação do lado do cliente para proteger os dados que enviar para o armazenamento
Outra opção que ajuda a garantir que os dados estão seguros durante a transferência entre uma aplicação cliente e o armazenamento é a encriptação do lado do cliente. Os dados são encriptados antes de serem transferidos para o armazenamento do Azure. Quando recuperar os dados do armazenamento do Azure, os dados são desencriptados depois de serem recebido no lado do cliente. Apesar dos dados são encriptados passando pela conexão, recomendamos que também utilizar HTTPS, porque esta tem verificações de integridade de dados incorporadas que ajudam a mitigar erros de rede que afetam a integridade dos dados.

Encriptação do lado do cliente também é um método para encriptar os dados em repouso, pois os dados são armazenados em sua forma criptografada. Vamos falar sobre isso mais detalhadamente na seção sobre [encriptação em repouso](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Encriptação inativa
Existem três recursos do Azure que fornecem a encriptação em repouso. O Azure Disk Encryption é utilizado para encriptar os discos de SO e dados em máquinas de virtuais de IaaS. Encriptação do lado do cliente e o SSE são usados para criptografar dados no armazenamento do Azure. 

Embora seja possível usar a encriptação do lado do cliente para encriptar os dados em trânsito (que também é armazenado no seu formato encriptado no armazenamento), poderá preferir utilizar HTTPS durante a transferência e ter alguma maneira para os dados sejam encriptados automaticamente quando esta está armazenada. Existem duas formas de fazê-lo – Azure Disk Encryption e SSE. Um é utilizado para encriptar diretamente os dados em discos de SO e os dados utilizados por VMs e a outra é usada para criptografar dados escritos no armazenamento de Blobs do Azure.

### <a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)

O SSE está ativado para todas as contas de armazenamento e não pode ser desabilitado. O SSE encripta automaticamente os dados ao escrevê-lo ao armazenamento do Azure. Quando ler os dados do armazenamento do Azure, estes são desencriptados pelo armazenamento do Azure antes de ser devolvido. O SSE permite-lhe proteger os seus dados sem ter de modificar o código ou adicione o código para todas as aplicações.

Pode utilizar chaves geridas pela Microsoft ou suas próprias chaves personalizadas. A Microsoft gera chaves geridas e lida com o respetivo armazenamento seguro, bem como seus rotação regular, como definido pela diretiva interna da Microsoft. Para obter mais informações sobre como utilizar chaves personalizadas, consulte [encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

O SSE encripta automaticamente dados em todos os escalões de desempenho (Standard e Premium), todos os modelos de implementação (Azure Resource Manager e Clássico) e todos os serviços de Armazenamento do Azure (Blob, Fila, Tabela e Ficheiro). 

### <a name="client-side-encryption"></a>Encriptação do lado do cliente
Mencionamos a encriptação do lado do cliente ao abordar a criptografia de dados em trânsito. Esta funcionalidade permite-lhe encriptar programaticamente os dados num aplicativo de cliente antes de enviá-la pela rede para ser escrita no armazenamento do Azure e para desencriptar programaticamente os seus dados após recuperá-lo a partir do armazenamento do Azure.

Isso fornece encriptação em trânsito, mas também fornece a funcionalidade de encriptação em repouso. Embora os dados são encriptados em trânsito, recomendamos ainda através de HTTPS para aproveitar as verificações de integridade de dados internos que ajudam a minimizar erros de rede que afetam a integridade dos dados.

Um exemplo de onde pode utilizar esta opção é se tiver uma aplicação web que armazena blobs e blobs de recupera e pretende que a aplicação e os dados para ser o mais segura possível. Nesse caso, usaria encriptação do lado do cliente. O tráfego entre o cliente e o serviço de Blobs do Azure contém o recurso criptografado, e ninguém pode interpretar os dados em trânsito e reconstitui-lo nos blobs de privada.

Encriptação do lado do cliente está incorporada no Java e as bibliotecas de cliente de armazenamento do .NET, que por sua vez, utilize as APIs do Azure Key Vault, tornando mais fácil de implementar. O processo de encriptação e desencriptação de dados utiliza a técnica de envelope e armazena metadados usados pela encriptação em cada objeto de armazenamento. Por exemplo, para blobs, ele armazena-a nos metadados do blob, enquanto para filas, ele adiciona-a cada mensagem de fila.

Para a encriptação em si, pode gerar e gerir as suas próprias chaves de encriptação. Também pode utilizar chaves geradas pela biblioteca de cliente de armazenamento do Azure ou pode ter a gerar as chaves do Azure Key Vault. Pode armazenar suas chaves de encriptação no seu armazenamento de chaves no local, ou pode armazenar um Azure Key Vault. O Azure Key Vault permite-lhe conceder acesso a segredos no Cofre de chaves do Azure para utilizadores específicos que utilizam o Azure Active Directory. Isso significa que não apenas a qualquer pessoa pode ler o Cofre de chaves do Azure e obter as chaves que estiver a utilizar para a encriptação do lado do cliente.

#### <a name="resources"></a>Recursos
* [Encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Este artigo mostra como utilizar a encriptação do lado do cliente com o Azure Key Vault, incluindo como criar a KEK e armazená-los no cofre com o PowerShell.
* [Encriptação do lado do cliente e o Azure Key Vault para o armazenamento do Microsoft Azure](../storage-client-side-encryption.md)

  Este artigo fornece uma explicação de encriptação do lado do cliente e fornece exemplos de como utilizar a biblioteca de cliente de armazenamento para encriptar e desencriptar recursos a partir de quatro serviços de armazenamento. Também fala sobre o Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Com o Azure Disk Encryption para encriptar discos utilizados pelas suas máquinas virtuais
O Azure Disk Encryption é um novo recurso. Esta funcionalidade permite-lhe encriptar os discos de SO e discos de dados utilizados pela máquina Virtual IaaS. Para Windows, as unidades são encriptadas com a tecnologia de encriptação do BitLocker de norma da indústria. Para o Linux, os discos são encriptados com a tecnologia de DM-Crypt. Isso está integrado com o Azure Key Vault, para que possa controlar e gerir as chaves de encriptação de disco.

A solução suporta os seguintes cenários para IaaS VMs quando forem ativadas no Microsoft Azure:

* Integração com o Cofre de chaves do Azure
* Escalão Standard VMs: [D, DS, G, GS e assim por diante série IaaS VMs](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Ativar a encriptação em VMs de IaaS de Linux e Windows
* A desativação da encriptação no SO e dados de unidades para VMs de IaaS do Windows
* Desativar a encriptação em unidades de dados para VMs de IaaS Linux
* Ativar a encriptação em VMs de IaaS que estão a executar o SO de cliente do Windows
* Ativar a encriptação em volumes com caminhos de montagem
* Ativar a encriptação em VMs do Linux que estão configurados com disco repartição (RAID) utilizando mdadm
* Ativar a encriptação em VMs do Linux ao utilizar LVM para discos de dados
* Ativar a encriptação em VMs do Windows que são configuradas através de espaços de armazenamento
* São suportadas todas as regiões públicas do Azure

A solução não suporta os seguintes cenários, recursos e tecnologia na versão:

* VMs de IaaS de escalão básico
* A desativação da encriptação numa unidade do sistema operacional para VMs de IaaS Linux
* VMs de IaaS que são criadas com o método de criação de VM clássico
* Integração com o seu serviço de gestão de chaves no local
* Ficheiros do Azure (sistema de ficheiros partilhados), o sistema de ficheiros de rede (NFS), volumes dinâmicos e VMs do Windows que estão configurados com sistemas RAID baseados em software


> [!NOTE]
> Encriptação de disco do SO Linux é atualmente suportada em distribuições de Linux seguintes: RHEL 7.2 CentOS 7.2n e Ubuntu 16.04.
>
>

Esta funcionalidade garante que todos os dados nos discos da máquina virtual são encriptados em inatividade no armazenamento do Azure.

#### <a name="resources"></a>Recursos
* [Azure Disk Encryption para VMs de Linux IaaS e do Windows](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparação entre o Azure Disk Encryption, o SSE e encriptação do lado do cliente

#### <a name="iaas-vms-and-their-vhd-files"></a>VMs de IaaS e os respetivos ficheiros VHD

Para discos de dados utilizados por VMs de IaaS, recomenda-se do Azure Disk Encryption. Se criar uma VM com discos não geridos através de uma imagem do Azure Marketplace, o Azure efetua uma [superficialmente cópia](https://en.wikipedia.org/wiki/Object_copying) da imagem para o armazenamento de conta no armazenamento do Azure e ele não está encriptada, mesmo se tiver ativado o SSE. Depois que ele cria a VM e começa a atualizar a imagem, o SSE começará a encriptar os dados. Por esse motivo, é melhor utilizar o Azure Disk Encryption em VMs com discos não geridos criados a partir de imagens no Azure Marketplace se deseja que eles totalmente encriptado. Se criar uma VM com Managed Disks, o SSE encripta todos os dados por predefinição, com chaves de plataforma gerenciada. 

Se colocar uma VM encriptada para o Azure no local, será capaz de carregar as chaves de encriptação para o Azure Key Vault e continuar a utilizar a encriptação para essa VM em que estava a utilizar no local. O Azure Disk Encryption está ativado para processar este cenário.

Se tiver o VHD não encriptada no local, pode carregá-lo para a galeria como uma imagem personalizada e aprovisionar uma VM a partir do mesmo. Se faz isso usando os modelos do Resource Manager, pode fazer para ativar o Azure Disk Encryption quando é arrancado a VM.

Quando adiciona um disco de dados e montá-la na VM, pode ativar o Azure Disk Encryption desse disco de dados. Ele vai encriptar desse disco de dados localmente em primeiro lugar, e, em seguida, a camada de modelo de implementação clássica fará uma gravação lenta no armazenamento para que o conteúdo de armazenamento é encriptado.

#### <a name="client-side-encryption"></a>Encriptação do lado do cliente
Encriptação do lado do cliente é o método mais seguro de criptografar seus dados, porque esta encripta os dados antes de trânsito.  No entanto, ele exige que adicionar código para as aplicações com o armazenamento, que pode não querer fazer. Nesses casos, pode utilizar o HTTPS para proteger os dados em trânsito. Quando os dados atingirem o armazenamento do Azure, está encriptado pelo SSE.

Com a encriptação do lado do cliente, pode criptografar entidades da tabela, fila de mensagens e os blobs. 

Encriptação do lado do cliente é gerenciada totalmente pelo aplicativo. Esta é a abordagem mais segura, mas exige que faça alterações programáticas ao seu aplicativo e colocar os processos de gestão de chaves no local. A usa quando pretender que a segurança adicional durante o trânsito e pretender que os seus dados armazenados sejam encriptados.

Encriptação do lado do cliente é mais carga no cliente e a considerar para isso em seus planos de escalabilidade, especialmente se estiver a encriptar e a transferência de uma grande quantidade de dados.

#### <a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)

O SSE é gerido pelo armazenamento do Azure. O SSE não fornece para garantir a segurança dos dados em trânsito, mas criptografa os dados como ele é escrito para o armazenamento do Azure. O SSE não afeta o desempenho do Armazenamento do Azure.

Pode criptografar qualquer tipo de dados do SSE a utilizar a conta de armazenamento (blobs de blocos, blobs de acréscimo, blobs de páginas, dados de tabela, os dados de fila e arquivos).

Se tiver um arquivo ou de uma biblioteca de arquivos VHD que usar como base para a criação de novas máquinas virtuais, pode criar uma nova conta de armazenamento e, em seguida, carregue os ficheiros VHD para essa conta. Esses ficheiros VHD serão encriptados pelo armazenamento do Azure.

Se tiver ativado para os discos numa VM do Azure Disk Encryption, todos os dados recentemente gravados são encriptados pelo SSE e ao Azure Disk Encryption.

## <a name="storage-analytics"></a>Análise de Armazenamento
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>A utilização de análise de armazenamento para monitorizar o tipo de autorização
Para cada conta de armazenamento, pode ativar a análise de armazenamento do Azure efetuar o registo e armazenar dados de métricas. Esta é uma ótima ferramenta para utilizar quando pretende verificar as métricas de desempenho de uma conta de armazenamento, ou precisar de resolver uma conta de armazenamento porque está a ter problemas de desempenho.

Outra parte dos dados, que pode ver nos registos de análise de armazenamento é o método de autenticação utilizado por qualquer pessoa quando acedem de armazenamento. Por exemplo, com o armazenamento de BLOBs, pode ver se usados uma assinatura de acesso partilhado ou as chaves de conta de armazenamento, ou se o blob acedido foi público.

Isto pode ser útil se rigidamente são protege o acesso ao armazenamento. Por exemplo, no armazenamento de BLOBs pode definir todos os contentores para particular e implementar a utilização de um serviço SAS ao longo de seus aplicativos. Em seguida, pode verificar os registos regularmente para ver se os blobs são acedidos utilizando as chaves de conta de armazenamento, que podem indicar uma violação de segurança, ou se os blobs sejam públicos, mas eles não devem ser.

#### <a name="what-do-the-logs-look-like"></a>O que o aspeto dos registos?
Depois de ativar as métricas da conta de armazenamento e dados de análise de registo através do portal do Azure, irão iniciar a acumular-se rapidamente. O registo e métricas para cada serviço é separado; o registo é escrito apenas quando houver atividade nessa conta de armazenamento, enquanto as métricas serão registadas a cada minuto, hora a hora ou todos os dias, dependendo de como o configurar.

Os registos são armazenados nos blobs de blocos num contentor com o nome $logs na conta de armazenamento. Este contentor é criado automaticamente quando a análise de armazenamento é ativada. Depois de criar este contentor, não é possível eliminá-lo, apesar de poder eliminar o seu conteúdo.

No contentor $logs, existe uma pasta para cada serviço e, em seguida, há subpastas para a ano/mês/dia/hora. Em hora, os registos são numerados. Este é o que a estrutura de diretórios terá o seguinte aspeto:

![Vista de ficheiros de registo](./media/storage-security-guide/image1.png)

Todos os pedidos ao armazenamento do Azure é registado. Este é um instantâneo de um ficheiro de registo, que mostra os poucos campos primeiro.

![Instantâneo de um ficheiro de registo](./media/storage-security-guide/image2.png)

Pode ver o que pode utilizar os registos para controlar a qualquer tipo de chamadas para uma conta de armazenamento.

#### <a name="what-are-all-of-those-fields-for"></a>Quais são todos esses campos para?
Há um artigo listado nos recursos abaixo que fornece a lista de campos muitos os registos e o que são utilizados para. Eis a lista de campos na ordem:

![Instantâneo de campos num ficheiro de registo](./media/storage-security-guide/image3.png)

Estamos interessados em entradas para GetBlob, e como estiverem autorizadas, por isso, precisamos procure entradas com "Get Blob" de tipo de operação e verifique o estado do pedido (quarta</sup> coluna) e o tipo de autorização (oitavo</sup> coluna).

Por exemplo, as primeiras linhas na listagem acima, o estado do pedido é "Êxito" e o tipo de autorização é "autenticado". Isso significa que o pedido foi autorizado a utilizar a chave de conta de armazenamento.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Como é o acesso ao meu blobs a ser autorizado?
Temos três casos que estamos interessados em.

1. O blob é público e é acedido através de um URL sem uma assinatura de acesso partilhado. Neste caso, o estado do pedido é "AnonymousSuccess" e o tipo de autorização é "anónimo".

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. O blob é privado e foi utilizado com uma assinatura de acesso partilhado. Neste caso, o estado do pedido é "SASSuccess" e o tipo de autorização é "sas".

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. O blob é privado e a chave de armazenamento foi utilizada para aceder ao mesmo. Neste caso, o estado do pedido é "**sucesso**"e o tipo de autorização é"**autenticado**".

   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Pode utilizar o Microsoft Message Analyzer para ver e analisar estes registos. Inclui capacidades de pesquisa e filtro. Por exemplo, pode querer pesquisar instâncias de GetBlob para ver se a utilização é o esperado, ou seja, para garantir que alguém é não aceder à sua conta de armazenamento inadequadamente.

#### <a name="resources"></a>Recursos
* [Análise de Armazenamento](../storage-analytics.md)

  Este artigo é uma descrição geral da análise de armazenamento e como para ativá-las.
* [Formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Este artigo ilustra o formato de registo de análise de armazenamento e fornece detalhes sobre os campos disponíveis nele, incluindo tipo de autenticação, que indica o tipo de autenticação utilizada para o pedido.
* [Monitorizar uma conta de armazenamento no portal do Azure](../storage-monitor-storage-account.md)

  Este artigo mostra como configurar a monitorização de métricas e registo de uma conta de armazenamento.
* [Resolução de problemas de ponto-a-ponto utilizando as métricas de armazenamento do Azure e o Registro em log, o AzCopy e o analisador de mensagens](../storage-e2e-troubleshooting.md)

  Este artigo fala sobre a resolução de problemas com a análise de armazenamento e mostra como utilizar o Microsoft Message Analyzer.
* [Guia de operação do analisador de mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)

  Este artigo é a referência para o Microsoft Message Analyzer e inclui links para um tutorial de início rápido e resumo de funcionalidades.

## <a name="cross-origin-resource-sharing-cors"></a>Partilha de Recursos Transversais à Origem (CORS)
### <a name="cross-domain-access-of-resources"></a>Acesso entre domínios de recursos
Quando um navegador da web em execução num domínio faz um pedido HTTP para um recurso de um domínio diferente, isso é chamado um pedido HTTP de várias origens. Por exemplo, uma página HTML atendida a partir de contoso.com faz um pedido para um jpeg alojado em fabrikam.blob.core.windows.net. Por motivos de segurança, navegadores restringem os pedidos HTTP de várias origens iniciados a partir de scripts, como JavaScript. Isso significa que quando algum código JavaScript numa página da web em contoso.com solicita que jpeg no fabrikam.blob.core.windows.net, o navegador não permitirá que o pedido.

O que isso tem a ver com o armazenamento do Azure? Bem, se estiver armazenando recursos estáticos, tais como ficheiros de dados JSON ou XML no armazenamento de BLOBs através de uma conta de armazenamento chamado Fabrikam, o domínio para os recursos serão fabrikam.blob.core.windows.net e a aplicação web do contoso.com não será capaz de acessá-los usando JavaScript porque os domínios são diferentes. Isto também se aplica se estiver a tentar chamar um dos serviços de armazenamento do Azure, como o armazenamento de tabelas – que retornam dados JSON para serem processados pelo cliente JavaScript.

#### <a name="possible-solutions"></a>Possíveis soluções
Uma forma de resolver este problema é atribuir um domínio personalizado, como "storage.contoso.com" a fabrikam.blob.core.windows.net. O problema é que só pode atribuir esse domínio personalizado para uma conta de armazenamento. E se os recursos são armazenados em várias contas de armazenamento?

Outra forma de resolver este problema é que o aplicativo web que atue como proxy para as chamadas de armazenamento. Isso significa que se estiver a carregar um ficheiro para o armazenamento de BLOBs, a aplicação web teria gravá-lo localmente e, em seguida, copie-o para o armazenamento de BLOBs ou seria ler tudo na memória e, em seguida, escrevê-lo para o armazenamento de Blobs. Em alternativa, poderia escrever um aplicativo de web dedicados (por exemplo, uma API Web), que carrega os ficheiros localmente e escreve-as para o armazenamento de Blobs. De qualquer forma, terá de conta para essa função, quando precisa de determinar a escalabilidade.

#### <a name="how-can-cors-help"></a>Como o CORS pode ajudar?
O armazenamento do Azure permite-lhe ativar o CORS – entre a partilha de recursos de origem. Para cada conta de armazenamento, pode especificar domínios que podem aceder aos recursos nessa conta de armazenamento. Por exemplo, no nosso caso descrito acima, podemos ativar o CORS na conta de armazenamento fabrikam.blob.core.windows.net e configurá-lo para permitir o acesso a contoso.com. Em seguida, o contoso.com de aplicativo web pode acessar diretamente os recursos na fabrikam.blob.core.windows.net.

Uma coisa a observar é que o CORS permite o acesso, mas ele não fornece autenticação, o que é necessária para todos os não-acesso público dos recursos de armazenamento. Isso significa que só pode aceder a blobs se eles são públicos ou incluir uma assinatura de acesso partilhado dando-lhe a permissão adequada. Tabelas, filas e ficheiros não tem nenhum acesso público e exigem uma SAS.

Por predefinição, o CORS está desativada em todos os serviços. Pode ativar a CORS utilizando a API REST ou biblioteca de cliente do armazenamento para chamar um dos métodos para definir as políticas do serviço. Quando faz isso, pode incluir uma regra CORS, que está em XML. Eis um exemplo de uma regra CORS que foi definido com a operação definir propriedades do serviço para o serviço de BLOBs para uma conta de armazenamento. Pode executar essa operação usando a biblioteca de cliente de armazenamento ou as APIs REST do armazenamento do Azure.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Eis o que significa que cada linha:

* **AllowedOrigins** isso informa ao quais os domínios que não correspondente podem pedir e receber dados a partir do serviço de armazenamento. Isso significa que contoso.com e fabrikam.com podem pedir a dados de armazenamento de BLOBs para uma conta de armazenamento específico. Pode também definir esta opção para um caráter universal (\*) para permitir que todos os domínios acedam à pedidos.
* **AllowedMethods** esta é a lista de métodos (verbos de pedido HTTP) que podem ser utilizados quando efetua o pedido. Neste exemplo, apenas PUT e GET são permitidas. Pode definir esta opção para um caráter universal (\*) para permitir que todos os métodos para ser utilizado.
* **AllowedHeaders** trata os cabeçalhos de pedido que o domínio de origem pode especificar quando efetua o pedido. Neste exemplo, todos os cabeçalhos de metadados a partir do x-ms-metadados, x-ms-meta-destino e x-ms-meta-abc, são permitidas. O caráter universal (\*) indica que qualquer início de cabeçalho com o prefixo especificado é permitido.
* **ExposedHeaders** isso informa ao quais cabeçalhos de resposta devem ser expostos pelo browser para o emissor do pedido. Neste exemplo, qualquer cabeçalho começando com "x-ms - meta-" será exposta.
* **MaxAgeInSeconds** esta é a quantidade máxima de tempo que um navegador colocarão em cache solicitação de simulação de opções. (Para obter mais informações sobre a solicitação de simulação, verifique o primeiro artigo abaixo.)

#### <a name="resources"></a>Recursos
Para obter mais informações sobre o CORS e como para ativá-la, veja estes recursos.

* [Recursos de várias origens (CORS) suporte para os serviços de armazenamento do Azure no Azure.com de partilha](../storage-cors-support.md)

  Este artigo fornece uma visão geral do CORS e como definir as regras para os serviços de armazenamento diferente.
* [Recursos de várias origens (CORS) suporte para os serviços de armazenamento do Azure no MSDN de partilha](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Esta é a documentação de referência para o suporte CORS para os serviços de armazenamento do Azure. Isso tem ligações para artigos que aplicar a cada serviço de armazenamento e mostra um exemplo e explica cada elemento no arquivo de CORS.
* [Armazenamento do Microsoft Azure: Apresentando o CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Esta é uma ligação para o artigo de blogue inicial, apresentamos o CORS e Mostrar como usá-lo.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Perguntas mais frequentes sobre a segurança de armazenamento do Azure
1. **Como eu posso verificar a integridade dos blobs que eu estou transferência dentro ou fora do armazenamento do Azure se eu não é possível utilizar o protocolo HTTPS?**

   Se por algum motivo, que terá de utilizar HTTP em vez de HTTPS e estiver trabalhando com os blobs de blocos, pode utilizar a verificação de MD5 para ajudar a verificar a integridade dos blobs a ser transferidos. Isso ajudará com proteção contra erros de camada de rede/transporte, mas não necessariamente com ataques de intermediários.

   Se puder usar HTTPS, que fornece segurança de nível de transporte, em seguida, usando a verificação de MD5 é redundante e desnecessários.

   Para obter mais informações, consulte a [descrição geral do Azure Blob MD5](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **E quanto a conformidade FIPS para o US Governo?**

   Os Estados Unidos Federal Information Processing Standard (FIPS) define os algoritmos criptográficos aprovados para utilização por dos EUA Sistemas de computador do Governo Federal para a proteção de dados confidenciais. Ativar FIPS modo num servidor do Windows ou o ambiente de trabalho informa ao sistema operacional que devem ser usados apenas validados FIPS algoritmos criptográficos. Se um aplicativo usa algoritmos de não conformidade, interromperá os aplicativos. Versões de com o.NET Framework 4.5.2 ou superior, a aplicação muda automaticamente os algoritmos de criptografia para utilizar algoritmos compatíveis com FIPS quando o computador está no modo FIPS.

   Microsoft deixa para cada cliente para decidir se pretende ativar o modo FIPS. Acreditamos que não existe nenhum motivo convincente para os clientes que não são sujeitos a regulamentos de administração pública para ativar o modo FIPS por predefinição.

### <a name="resources"></a>Recursos
* [Por que motivo está não recomendamos "Modo FIPS" mais](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Este artigo de blogue fornece uma descrição geral das FIPS e explica por que motivo não ativar modo FIPS por predefinição.
* [O FIPS 140 validação](https://technet.microsoft.com/library/cc750357.aspx)

  Este artigo fornece informações sobre como módulos criptográficos e produtos da Microsoft está em conformidade com a norma FIPS para o US Governo Federal.
* ["Criptografia de sistema: Utilizar algoritmos compatíveis com FIPS para encriptação, hash e assinatura"no Windows XP e em versões posteriores do Windows o efeitos de definições de segurança](https://support.microsoft.com/kb/811833)

  Este artigo fala sobre a utilização do modo FIPS no computadores antigos do Windows.
