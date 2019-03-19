---
title: Guia de segurança de armazenamento de geração 2 do Data Lake Storage do Azure | Documentos da Microsoft
description: Detalhes dos muitos métodos de proteger o armazenamento do Azure, incluindo mas não limitado a RBAC e a encriptação do serviço de armazenamento
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/07/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 22b070e6d70208057c85ad6a2322cc440d12a0fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008206"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Guia de segurança de geração 2 de armazenamento do Data Lake do Azure

O Azure Data Lake Storage geração 2, é um conjunto de recursos incorporados em contas de armazenamento do Azure. Como tal, todas as referências neste artigo destinam-se para uma conta de armazenamento do Azure com o espaço de nomes hierárquico ativado (capacidades de geração 2 de armazenamento do Data Lake).

- Todos os dados escritos no armazenamento do Azure são automaticamente encriptados usando [Storage Service Encryption (SSE)](storage-service-encryption.md). Para obter mais informações, consulte [anunciando encriptação predefinido para os Blobs do Azure, ficheiros, tabelas e armazenamento de filas](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) e o controlo de acesso baseado em funções (RBAC) são suportadas para o armazenamento do Azure para operações de gestão de recursos e operações de dados, da seguinte forma:
    - Pode atribuir funções RBAC de âmbito para a conta de armazenamento para entidades de segurança e utilize o Azure AD para autorizar as operações de gestão de recursos, tais como a gestão de chaves.
    - Integração do Azure AD é suportada para operações de dados no armazenamento do Azure. Pode atribuir funções RBAC no âmbito de uma subscrição, grupo de recursos, conta de armazenamento ou um sistema de ficheiros individual a uma entidade de segurança ou uma identidade gerida para recursos do Azure. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory](storage-auth-aad.md).
- Acesso delegado para os objetos de dados no armazenamento do Azure pode ser concedido usando [assinaturas de acesso partilhado](../storage-dotnet-shared-access-signature-part-1.md).

Este artigo fornece uma visão geral de cada um desses recursos de segurança que podem ser utilizados com o armazenamento do Azure. Ligações são fornecidas para artigos que vão dar detalhes de cada funcionalidade, pode facilmente fazer ainda mais investigação em cada tópico.

Aqui estão os tópicos para serem abordadas neste artigo:

* [Segurança de plano de gestão](#management-plane-security) – proteger a sua conta de armazenamento

  O plano de gestão consiste nos recursos utilizados para gerir a sua conta de armazenamento. Esta secção abrange o modelo de implementação Azure Resource Manager e como utilizar o controlo de acesso baseado em funções (RBAC) para controlar o acesso às contas de armazenamento. Também aborda a gerir as chaves de conta de armazenamento e como regenerá-los.
* [Segurança do plano de dados](#data-plane-security) – proteger o acesso aos seus dados

  Nesta secção, vamos permitir o acesso aos objetos de dados reais na sua conta de armazenamento, ou seja, ficheiros e diretórios, utilizar assinaturas de acesso partilhado e políticas de acesso armazenado. Abordaremos SAS de nível de serviço e ao nível da conta SAS. Também veremos como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo utilizado para HTTPS e como revogar uma assinatura de acesso partilhado sem aguardar expire.
* [Encriptação em Trânsito](#encryption-in-transit)

Esta secção descreve como proteger dados ao transferi-lo para dentro ou fora de uma conta de armazenamento com o Data Lake Storage Gen2 ativada. Vamos falar sobre a utilização recomendada de HTTPS.

## <a name="management-plane-security"></a>Segurança de plano de gestão

O plano de gestão consiste em operações que afetam a própria conta de armazenamento. Por exemplo, pode criar ou eliminar uma conta de armazenamento, obter uma lista de contas de armazenamento numa subscrição, obter as chaves de conta de armazenamento ou voltar a gerar as chaves de conta de armazenamento.

Este guia concentra-se no modelo de Gestor de recursos de implantação, que é o meio para a criação de contas de armazenamento com recursos de geração 2 de armazenamento do Data Lake. Com as contas de armazenamento do Resource Manager, em vez de dando-acesso para a subscrição completa, pode controlar o acesso num nível mais finito para o plano de gestão com o controlo de acesso baseado em funções (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)

Vamos falar sobre o que é o RBAC e como pode usá-lo. Cada subscrição do Azure tem um Azure Active Directory. Os utilizadores, grupos e aplicações a partir desse diretório podem ser concedidas acesso para gerir os recursos na subscrição do Azure que utilizam o modelo de implementação do Resource Manager. Este tipo de segurança é referido como controlo de acesso baseado em funções (RBAC). Para gerir este acesso, utilize **controlo de acesso (IAM)** no portal do Azure.

Com o modelo do Resource Manager, coloque a conta de armazenamento num grupo e controlar o acesso recursos para o plano de gestão dessa conta de armazenamento específica com o Azure Active Directory. Por exemplo, pode dar aos utilizadores específicos a capacidade de aceder as chaves de conta de armazenamento, enquanto outros utilizadores podem ver informações sobre a conta de armazenamento, mas não é possível aceder as chaves de conta de armazenamento.

#### <a name="granting-access"></a>Conceder acesso

O acesso é concedido ao atribuir a função RBAC adequada para os utilizadores, grupos e aplicações, no âmbito da direita. Para conceder acesso para a subscrição completa, atribuir uma função ao nível da subscrição. Pode conceder acesso a todos os recursos num grupo de recursos ao conceder permissões para o grupo de recursos. Também pode atribuir funções específicas a recursos específicos, como contas de armazenamento.

Aqui estão os pontos principais que precisa saber sobre como utilizar o RBAC para acessar as operações de gestão de uma conta de armazenamento do Azure:

* Para que alguém tem permissão para aceder os objetos de dados na conta de armazenamento, pode dar-lhes permissão para ler as chaves de conta de armazenamento e que o utilizador, em seguida, pode utilizar essas chaves para aceder a ficheiros e diretórios.
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

Outra opção é colocar a chave de conta de armazenamento num [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) como um segredo e ter seus aplicativos obter a chave a partir daí. Em seguida, quando regenerar a chave e atualizar o Azure Key Vault, os aplicativos não precisarão de ser novamente implementada porque eles selecionará a nova chave do Cofre de chaves do Azure automaticamente. Pode ter a aplicação a ler a chave de cada vez que for preciso, ou cache na memória e se falhar quando utilizá-lo, obter a chave novamente de Cofre de chaves do Azure.

Também com o Azure Key Vault adiciona outro nível de segurança para as chaves de armazenamento. Se usar esse método, nunca terá o inseridos no código de chave de armazenamento num arquivo de configuração, o que remove essa Avenida de alguém o acesso às chaves sem permissão específica.

Outra vantagem de utilizar o Azure Key Vault é que também pode controlar o acesso para as suas chaves com o Azure Active Directory. Isso significa que pode conceder acesso a muitos dos aplicativos que precisam para obter as chaves do Azure Key Vault e saber que outras aplicações não conseguir aceder às chaves, sem conceder-lhes permissão especificamente.

> [!NOTE]
> A Microsoft recomenda utilizar apenas uma das chaves em todos os seus aplicativos ao mesmo tempo. Se utilizar a chave 1 em alguns locais e a chave 2 em outros, não será capaz de rodar as chaves sem algum aplicativo perder o acesso.

#### <a name="resources"></a>Recursos

* [Gerir definições de conta de armazenamento no portal do Azure](storage-account-manage.md)
* [Referência da API REST do Fornecedor de Recursos de Armazenamento do Azure](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Segurança de plano de dados
Segurança de plano de dados refere-se para os métodos usados para proteger os objetos de dados armazenados no armazenamento do Azure. Já vimos métodos para encriptar os dados e a segurança durante o trânsito dos dados, mas como fazer para informações sobre como controlar o acesso aos objetos?

Tem três opções para autorizar o acesso a objetos de dados no armazenamento do Azure, incluindo:

- Utilizar o Azure AD para autorizar o acesso a sistemas de ficheiros e filas. O Azure AD proporciona vantagens em relação a outras abordagens para autorização, incluindo a remover a necessidade de armazenar segredos no seu código. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory](storage-auth-aad.md). 
- As chaves de conta de armazenamento a utilizar para autorizar o acesso por meio da chave partilhada. Autorizar através de chave partilhada requer a armazenar as chaves de conta de armazenamento na sua aplicação, para que a Microsoft recomenda a utilização do Azure AD em vez disso, sempre que possível. Para aplicações de produção ou para autorizar o acesso a ficheiros e de tabelas do Azure, continue a utilizar a chave partilhada enquanto a integração do Azure AD está em pré-visualização.
- Utilização de assinaturas de acesso partilhado para conceder permissões controladas para objetos de dados específicos para um período de tempo específico.

Além de limitar o acesso por meio de autorização, também pode utilizar [Firewalls e redes virtuais](storage-network-security.md) para limitar o acesso à conta de armazenamento com base nas regras de rede.  Esta permite que a abordagem negar o acesso ao tráfego da internet pública e conceder acesso apenas a determinados redes virtuais do Azure ou da internet pública intervalos de endereços IP.

### <a name="storage-account-keys"></a>Chaves de Contas de Armazenamento

Chaves de conta de armazenamento são criadas pelo Azure que, juntamente com o nome da conta de armazenamento, pode ser usado para acessar os objetos de dados armazenados na conta de armazenamento de cadeias de 512 bits.

Por exemplo, pode ler arquivos. Muitas destas ações podem ser efetuadas através do portal do Azure, ou usando uma das muitas aplicações de Explorador de armazenamento. Também pode escrever código para usar a API REST para executar estas operações.

Como discutido na seção sobre o [segurança de plano de gestão](#management-plane-security), aceder às chaves de armazenamento para uma conta de armazenamento com o modelo Azure Resource Manager pode ser controlada por meio do controle de acesso baseado em funções (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como delegar acesso a objetos na sua conta com assinaturas de acesso partilhado e políticas de acesso armazenadas

Uma assinatura de acesso partilhado é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI que lhe permite delegar acesso a objetos de armazenamento e especificar limitações, como as permissões e o intervalo de data/hora de acesso.

Pode conceder acesso a ficheiros ou diretórios.

Poderia dar um cliente um token SAS podem utilizar para carregar imagens para um sistema de ficheiros no armazenamento de BLOBs bem como fornecer uma permissão de aplicação web para ler essas imagens. Em ambos os casos, há uma separação das preocupações – cada aplicativo pode receber apenas o acesso que necessitam para realizar suas tarefas. Isso é possível através da utilização de assinaturas de acesso partilhado.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Por que pretende utilizar assinaturas de acesso partilhado

Por que iria querer utilizar uma SAS em vez de apenas-lo a sua chave de conta de armazenamento, o que é muito mais fácil? -Lo a sua chave de conta de armazenamento é como a partilha as chaves da sua Unido de armazenamento. Ele concede acesso completo. Alguém poderia utilizar as suas chaves e carregar sua biblioteca de música todo para a conta de armazenamento. Eles também poderiam substituir os ficheiros infetados com vírus versões ou roubar os seus dados. Dar acesso ilimitado à sua conta de armazenamento é algo que não deve ser levada a sério.

Com assinaturas de acesso partilhado, pode dar um cliente apenas as permissões necessárias para uma quantidade limitada de tempo. Por exemplo, se alguém está a carregar um ficheiro à sua conta, pode conceder-lhes acesso de escrita para o tempo suficiente carregar o ficheiro (consoante o tamanho do ficheiro, é claro). E se mudar de ideias, pode revogar o acesso.

Além disso, pode especificar que os pedidos efetuados com uma SAS estão limitados a um determinado endereço IP ou intervalo de endereços IP externo para o Azure. Também pode exigir que os pedidos são efetuados utilizando um protocolo específico (HTTPS ou HTTP/HTTPS). Isso significa que se quiser apenas permitir o tráfego HTTPS, que pode definir apenas o protocolo necessário para HTTPS e o tráfego HTTP será bloqueado.

#### <a name="definition-of-a-shared-access-signature"></a>Definição de uma assinatura de acesso partilhado

Uma assinatura de acesso partilhado é um conjunto de parâmetros de consulta anexado ao URL apontando para o recurso

que fornece informações sobre o acesso permitido e o período de tempo para o qual o acesso é permitido. Eis um exemplo; Este URI fornece acesso de leitura para um blob para cinco minutos. Parâmetros de consulta SAS tem de ser codificados de URL, por exemplo, % 3A para dois pontos (:) ou % 20 para um espaço.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
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

* Uma SAS de nível de serviço pode ser utilizada para aceder a recursos específicos numa conta de armazenamento. Alguns exemplos disso são a obter uma lista de arquivos num sistema de ficheiros ou transferir um ficheiro.
* Uma SAS de nível de conta pode ser utilizada para aceder a algo que pode servir-se para uma SAS de nível de serviço. Além disso, ele pode permitir que as opções para recursos que não são permitidas com uma SAS de nível de serviço, como a capacidade de criar sistemas de ficheiros.

#### <a name="creating-a-sas-uri"></a>Criar um URI de SAS

1. Pode criar um URI a pedido, definindo todos os parâmetros de consulta cada vez.

   Essa abordagem é flexível, mas se tiver um conjunto lógico de parâmetros que são semelhantes a cada vez, através de uma política de acesso armazenadas é uma idéia melhor.
2. Pode criar uma política de acesso armazenadas para um sistema de ficheiros completo, partilha de ficheiros, tabela ou fila. Em seguida, pode utilizar isso como base para os URIs de SAS que cria. Permissões com base nas políticas de acesso armazenadas podem ser facilmente revogadas. Pode ter até cinco políticas definidas em cada sistema de ficheiros, fila, tabela ou partilha de ficheiros.

   Por exemplo, se fosse fazer muitas pessoas, leia os blobs num sistema de ficheiros específico, foi possível criar uma política de acesso armazenadas que diz "dê acesso de leitura" e outras definições que serão os mesmos cada vez. Em seguida, pode criar um URI de SAS, utilizando as definições da política de acesso armazenadas e especificando a data/hora de expiração. A vantagem disso é que não precisa de toda vez especificar todos os parâmetros de consulta.

#### <a name="revocation"></a>Revogação

Suponha que a SAS foi comprometida ou, se pretender alterá-lo devido a requisitos de conformidade a normas ou de segurança corporativa. Como revogar o acesso a um recurso com essa SAS? Depende de como criou o URI de SAS.

Se estiver a utilizar o ad hoc URIs, tem três opções. Pode emitir tokens SAS com políticas de expiração curto e aguarde que a SAS expire. Pode mudar o nome ou eliminar o recurso (supondo que o token foi no âmbito de um único objeto). Pode alterar as chaves de conta de armazenamento. Essa última opção pode ter um impacto significativo, dependendo de quantos serviços utilizar essa conta de armazenamento e provavelmente não é algo que pretende fazer sem algum planejamento.

Se estiver a utilizar uma SAS derivada de uma política de acesso armazenadas, pode remover o acesso ao revogar a política de acesso armazenadas – só pode alterá-lo para que já expirou ou pode removê-lo completamente. Isso entra em vigor imediatamente e invalida todas as SAS criados com essa política de acesso armazenado. A atualizar ou remover a política de acesso armazenadas podem aceder a esse sistema de ficheiros específico, partilha de ficheiros, de pessoas de impacto de tabela ou fila através de SAS, mas se os clientes são escritos para que eles solicitar uma SAS novo quando antigo se torna inválido, isso funcionará bem.

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

## <a name="encryption-at-rest"></a>Encriptação inativa

### <a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)

O SSE está ativado para todas as contas de armazenamento e não pode ser desabilitado. O SSE encripta automaticamente os dados ao escrevê-lo ao armazenamento do Azure. Quando ler os dados do armazenamento do Azure, estes são desencriptados pelo armazenamento do Azure antes de ser devolvido. O SSE permite-lhe proteger os seus dados sem ter de modificar o código ou adicione o código para todas as aplicações.

Pode utilizar chaves geridas pela Microsoft ou suas próprias chaves personalizadas. A Microsoft gera chaves geridas e lida com o respetivo armazenamento seguro, bem como seus rotação regular, como definido pela diretiva interna da Microsoft. Para obter mais informações sobre como utilizar chaves personalizadas, consulte [encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).
