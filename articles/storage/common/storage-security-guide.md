---
title: Guia de segurança do armazenamento do Azure | Microsoft Docs
description: Detalha os muitos métodos de proteção do armazenamento do Azure, incluindo, mas não se limitando a RBAC, Criptografia do Serviço de Armazenamento, criptografia do lado do cliente, SMB 3,0 e Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 72e695762f2e45309787e6f62fa97aae4c959f34
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598097"
---
# <a name="azure-storage-security-guide"></a>Guia de segurança do armazenamento do Azure

O armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que juntos permitem que os desenvolvedores criem aplicativos seguros:

- Todos os dados (incluindo metadados) gravados no armazenamento do Azure são criptografados automaticamente usando o [criptografia do serviço de armazenamento (SSE)](storage-service-encryption.md). Para obter mais informações, consulte [anunciando criptografia padrão para BLOBs do Azure, arquivos, tabela e armazenamento de fila](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- O Azure Active Directory (Azure AD) e o RBAC (controle de acesso baseado em função) têm suporte para o armazenamento do Azure para operações de gerenciamento de recursos e de dados, da seguinte maneira:   
    - Você pode atribuir funções RBAC com escopo à conta de armazenamento para entidades de segurança e usar o Azure AD para autorizar operações de gerenciamento de recursos, como o gerenciamento de chaves.
    - A integração do Azure AD tem suporte para operações de dados de BLOB e de fila. Você pode atribuir funções de RBAC com escopo para uma assinatura, grupo de recursos, conta de armazenamento ou um contêiner ou fila individual para uma entidade de segurança ou uma identidade gerenciada para recursos do Azure. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).   
- Os dados podem ser protegidos em trânsito entre um aplicativo e o Azure usando [criptografia do lado do cliente](../storage-client-side-encryption.md), HTTPS ou SMB 3,0.  
- O sistema operacional e OS discos de dados usados pelas máquinas virtuais do Azure podem ser criptografados usando [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
- O acesso delegado aos objetos de dados no armazenamento do Azure pode ser concedido usando uma assinatura de acesso compartilhado. Para obter mais informações, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando assinaturas de acesso compartilhado (SAS)](storage-sas-overview.md).

Este artigo fornece uma visão geral de cada um desses recursos de segurança que podem ser usados com o armazenamento do Azure. São fornecidos links para artigos que fornecerão detalhes de cada recurso para que você possa fazer mais investigações em cada tópico.

Aqui estão os tópicos a serem abordados neste artigo:

* [Segurança do plano de gerenciamento](#management-plane-security) – protegendo sua conta de armazenamento

  O plano de gerenciamento consiste nos recursos usados para gerenciar sua conta de armazenamento. Esta seção aborda o modelo de implantação Azure Resource Manager e como usar o RBAC (controle de acesso baseado em função) para controlar o acesso às suas contas de armazenamento. Ele também aborda o gerenciamento de suas chaves de conta de armazenamento e como gerá-las novamente.
* [Segurança do plano de dados](#data-plane-security) – proteção do acesso aos seus dados

  Nesta seção, veremos como permitir o acesso aos objetos de dados reais em sua conta de armazenamento, como BLOBs, arquivos, filas e tabelas, usando assinaturas de acesso compartilhado e políticas de acesso armazenado. Abordaremos SAS de nível de serviço e SAS de nível de conta. Também veremos como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo usado para HTTPS e como revogar uma assinatura de acesso compartilhado sem esperar que ela expire.
* [Encriptação em Trânsito](#encryption-in-transit)

  Esta seção discute como proteger os dados ao transferi-los para dentro ou para fora do armazenamento do Azure. Falaremos sobre o uso recomendado de HTTPS e a criptografia usada pelo SMB 3,0 para compartilhamentos de arquivos do Azure. Também veremos a criptografia do lado do cliente, que permite que você criptografe os dados antes que eles sejam transferidos para o armazenamento em um aplicativo cliente e descriptografe os dados depois que eles são transferidos para fora do armazenamento.
* [Encriptação Inativa](#encryption-at-rest)

  Falaremos sobre Criptografia do Serviço de Armazenamento (SSE), que agora é automaticamente habilitado para contas de armazenamento novas e existentes. Também veremos como você pode usar Azure Disk Encryption e explorar as diferenças básicas e os casos de criptografia de disco versus a criptografia do lado do cliente. Veremos rapidamente a conformidade com FIPS para computadores do governo dos EUA.
* Usando [análise de armazenamento](#storage-analytics) para auditar o acesso do armazenamento do Azure

  Esta seção discute como localizar informações nos logs de análise de armazenamento para uma solicitação. Vamos dar uma olhada nos dados reais do log da análise de armazenamento e ver como discernir se uma solicitação é feita com a chave da conta de armazenamento, com uma assinatura de acesso compartilhado ou anonimamente, e se ela foi bem-sucedida ou falhou.
* [Habilitando clientes baseados em navegador usando CORS](#cross-origin-resource-sharing-cors)

  Esta seção fala sobre como permitir o CORS (compartilhamento de recursos entre origens). Falaremos sobre o acesso entre domínios e como tratá-lo com os recursos de CORS incorporados ao armazenamento do Azure.

## <a name="management-plane-security"></a>Segurança do plano de gerenciamento
O plano de gerenciamento consiste em operações que afetam a própria conta de armazenamento. Por exemplo, você pode criar ou excluir uma conta de armazenamento, obter uma lista de contas de armazenamento em uma assinatura, recuperar as chaves da conta de armazenamento ou regenerar as chaves da conta de armazenamento.

Ao criar uma nova conta de armazenamento, você seleciona um modelo de implantação clássico ou do Resource Manager. O modelo clássico de criação de recursos no Azure permite apenas o acesso de tudo ou nada à assinatura e, por sua vez, à conta de armazenamento.

Este guia se concentra no modelo do Resource Manager, que é o meio recomendado para a criação de contas de armazenamento. Com as contas de armazenamento do Gerenciador de recursos, em vez de conceder acesso à assinatura inteira, você pode controlar o acesso em um nível mais finito ao plano de gerenciamento usando o RBAC (controle de acesso baseado em função).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger sua conta de armazenamento com RBAC (controle de acesso baseado em função)
Vamos falar sobre o que é o RBAC e como você pode usá-lo. Cada subscrição do Azure tem um Azure Active Directory. Usuários, grupos e aplicativos desse diretório podem receber acesso para gerenciar recursos na assinatura do Azure que usam o modelo de implantação do Gerenciador de recursos. Esse tipo de segurança é conhecido como RBAC (controle de acesso baseado em função). Para gerenciar esse acesso, você pode usar o [portal do Azure](https://portal.azure.com/), as [ferramentas de CLI do Azure](../../cli-install-nodejs.md), o [PowerShell](/powershell/azureps-cmdlets-docs)ou as [APIs REST do provedor de recursos de armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Com o modelo do Resource Manager, você coloca a conta de armazenamento em um grupo de recursos e controla o acesso ao plano de gerenciamento dessa conta de armazenamento específica usando Azure Active Directory. Por exemplo, você pode conceder a usuários específicos a capacidade de acessar as chaves da conta de armazenamento, enquanto outros usuários podem exibir informações sobre a conta de armazenamento, mas não podem acessar as chaves da conta de armazenamento.

#### <a name="granting-access"></a>Concedendo acesso
O acesso é concedido atribuindo a função RBAC apropriada a usuários, grupos e aplicativos, no escopo certo. Para conceder acesso a toda a assinatura, atribua uma função no nível de assinatura. Você pode conceder acesso a todos os recursos em um grupo de recursos concedendo permissões ao próprio grupo de recursos. Você também pode atribuir funções específicas a recursos específicos, como contas de armazenamento.

Aqui estão os principais pontos que você precisa saber sobre como usar o RBAC para acessar as operações de gerenciamento de uma conta de armazenamento do Azure:

* Quando você atribui acesso, basicamente atribui uma função à conta à qual você deseja ter acesso. Você pode controlar o acesso às operações usadas para gerenciar essa conta de armazenamento, mas não aos objetos de dados na conta. Por exemplo, você pode conceder permissão para recuperar as propriedades da conta de armazenamento (como redundância), mas não para um contêiner ou dados dentro de um contêiner dentro do armazenamento de BLOBs.
* Para que alguém tenha permissão para acessar os objetos de dados na conta de armazenamento, você pode conceder a eles permissão para ler as chaves da conta de armazenamento e esse usuário pode usar essas chaves para acessar os BLOBs, as filas, as tabelas e os arquivos.
* As funções podem ser atribuídas a uma conta de usuário específica, a um grupo de usuários ou a um aplicativo específico.
* Cada função tem uma lista de ações e não ações. Por exemplo, a função colaborador da máquina virtual tem uma ação de "listKeys" que permite que as chaves da conta de armazenamento sejam lidas. O colaborador tem "não ações", como atualizar o acesso para usuários no Active Directory.
* As funções para armazenamento incluem (mas não se limitam a) as seguintes funções:

  * Proprietário – eles podem gerenciar tudo, incluindo o acesso.
  * Colaborador – eles podem fazer qualquer coisa que o proprietário possa fazer, exceto atribuir acesso. Alguém com essa função pode exibir e regenerar as chaves da conta de armazenamento. Com as chaves da conta de armazenamento, eles podem acessar os objetos de dados.
  * Leitor – eles podem exibir informações sobre a conta de armazenamento, exceto segredos. Por exemplo, se você atribuir uma função com permissões de leitor na conta de armazenamento para alguém, ele poderá exibir as propriedades da conta de armazenamento, mas não poderá fazer nenhuma alteração nas propriedades ou exibir as chaves da conta de armazenamento.
  * Colaborador de conta de armazenamento – eles podem gerenciar a conta de armazenamento – eles podem ler os recursos e grupos de recursos da assinatura e criar e gerenciar implantações de grupo de recursos de assinatura. Eles também podem acessar as chaves da conta de armazenamento, o que, por sua vez, significa que podem acessar o plano de dados.
  * Administrador de acesso do usuário – eles podem gerenciar o acesso do usuário à conta de armazenamento. Por exemplo, eles podem conceder acesso de leitor a um usuário específico.
  * Colaborador da máquina virtual – eles podem gerenciar máquinas virtuais, mas não a conta de armazenamento à qual estão conectadas. Essa função pode listar as chaves da conta de armazenamento, o que significa que o usuário ao qual você atribui essa função pode atualizar o plano de dados.

    Para que um usuário crie uma máquina virtual, ele precisa ser capaz de criar o arquivo VHD correspondente em uma conta de armazenamento. Para fazer isso, eles precisam ser capazes de recuperar a chave da conta de armazenamento e passá-la para a API que cria a VM. Portanto, eles devem ter essa permissão para que possam listar as chaves da conta de armazenamento.
* A capacidade de definir funções personalizadas é um recurso que permite compor um conjunto de ações de uma lista de ações disponíveis que podem ser executadas em recursos do Azure.
* O usuário deve ser configurado no seu Azure Active Directory antes que você possa atribuir uma função a eles.
* Você pode criar um relatório de quem concedeu/revogou que tipo de acesso de/para quem e em qual escopo usando o PowerShell ou o CLI do Azure.

#### <a name="resources"></a>Recursos
* [Controlo de Acesso Baseado em Funções do Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  Este artigo explica o Controlo de Acesso Baseado em Funções do Azure Active Directory e como funciona.
* [RBAC: Funções Incorporadas](../../role-based-access-control/built-in-roles.md)

  Este artigo fornece detalhes sobre todas as funções internas disponíveis no RBAC.
* [Compreender a implementação do Resource Manager e a implementação clássica](../../azure-resource-manager/resource-manager-deployment-model.md)

  Este artigo explica a implantação do Resource Manager e os modelos de implantação clássicos e explica os benefícios de usar o Gerenciador de recursos e os grupos de recursos. Ele explica como os provedores de computação, rede e armazenamento do Azure funcionam no modelo do Resource Manager.
* [Gerir o Controlo de Acesso Baseado em Funções com a API REST](../../role-based-access-control/role-assignments-rest.md)

  Este artigo mostra como utilizar a API REST para gerir o RBAC.
* [Referência da API REST do Fornecedor de Recursos de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Esta referência de API descreve as APIs que você pode usar para gerenciar sua conta de armazenamento de forma programática.

* [Controlo de Acesso Baseado em Funções para o Microsoft Azure no Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Esta é uma ligação para um vídeo do Channel 9 da conferência Ignite 2015 MS. Nesta sessão, fala-se sobre as capacidades de gestão de acesso e de criação de relatórios no Azure e são exploradas as melhores práticas relativamente à proteção do acesso às subscrições do Azure com o Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Gerenciando suas chaves de conta de armazenamento
As chaves da conta de armazenamento são cadeias de caracteres de 512 bits criadas pelo Azure que, juntamente com o nome da conta de armazenamento, podem ser usadas para acessar os objetos de dados armazenados na conta de armazenamento, por exemplo, BLOBs, entidades em uma tabela, mensagens de fila e arquivos em um compartilhamento de arquivos do Azure. Controlar o acesso às chaves da conta de armazenamento controla o acesso ao plano de dados para essa conta de armazenamento.

Cada conta de armazenamento tem duas chaves referidas como "chave 1" e "chave 2" na [portal do Azure](https://portal.azure.com/) e nos cmdlets do PowerShell. Eles podem ser regenerados manualmente usando um dos vários métodos, incluindo, mas não se limitando a usar o [portal do Azure](https://portal.azure.com/), o PowerShell, o CLI do Azure ou programaticamente usando a biblioteca de cliente de armazenamento do .net ou a API REST dos serviços de armazenamento do Azure.

Há vários motivos para regenerar as chaves da conta de armazenamento.

* Você pode gerá-los regularmente por motivos de segurança.
* Você regenerará as chaves da conta de armazenamento se alguém fosse gerenciado para invadir um aplicativo e recuperar a chave que foi codificada ou salva em um arquivo de configuração, dando-lhes acesso completo à sua conta de armazenamento.
* Outro caso de regeneração de chave é se sua equipe está usando um aplicativo Gerenciador de Armazenamento que mantém a chave de conta de armazenamento e um dos membros da equipe deixa. O aplicativo continuaria funcionando, dando a eles acesso à sua conta de armazenamento depois que eles estiverem ausentes. Na verdade, esse é o principal motivo pelo qual eles criaram assinaturas de acesso compartilhado em nível de conta – você pode usar uma SAS de nível de conta em vez de armazenar as chaves de acesso em um arquivo de configuração.

#### <a name="key-regeneration-plan"></a>Plano de regeneração de chave
Você não quer apenas regenerar a chave que está usando sem algum planejamento. Se você fizer isso, poderá cortar todo o acesso a essa conta de armazenamento, o que pode causar grande interrupção. É por isso que há duas chaves. Você deve regenerar uma chave por vez.

Antes de regenerar suas chaves, verifique se você tem uma lista de todos os seus aplicativos que dependem da conta de armazenamento, bem como quaisquer outros serviços que você esteja usando no Azure. Por exemplo, se você estiver usando os serviços de mídia do Azure que dependem de sua conta de armazenamento, deverá ressincronizar as chaves de acesso com o serviço de mídia depois de regenerar a chave. Se você estiver usando qualquer aplicativo como um Gerenciador de armazenamento, também precisará fornecer as novas chaves para esses aplicativos. Se você tiver VMs cujos arquivos VHD são armazenados na conta de armazenamento, eles não serão afetados pela regeneração das chaves da conta de armazenamento.

Você pode regenerar suas chaves no portal do Azure. Depois que as chaves são regeneradas, podem levar até 10 minutos para serem sincronizadas entre os serviços de armazenamento.

Quando estiver pronto, aqui está o processo geral que detalha como você deve alterar sua chave. Nesse caso, a suposição é que você está usando a chave 1 no momento e, em vez disso, vai alterar tudo para usar a chave 2.

1. Regenerar a chave 2 para garantir que ela seja segura. Você pode fazer isso na portal do Azure.
2. Em todos os aplicativos em que a chave de armazenamento está armazenada, altere a chave de armazenamento para usar o novo valor da chave 2. Teste e publique o aplicativo.
3. Depois que todos os aplicativos e serviços estiverem ativos e em execução com êxito, gere novamente a chave 1. Isso garante que qualquer pessoa a quem você não tenha dado a nova chave não terá mais acesso à conta de armazenamento.

Se você estiver usando a chave 2 no momento, poderá usar o mesmo processo, mas inverter os nomes de chave.

Você pode migrar por alguns dias, alterando cada aplicativo para usar a nova chave e publicá-lo. Depois que todas elas forem concluídas, você deverá voltar e regenerar a chave antiga para que ela não funcione mais.

Outra opção é colocar a chave da conta de armazenamento em um [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) como um segredo e fazer com que seus aplicativos recuperem a chave a partir daí. Em seguida, ao regenerar a chave e atualizar o Azure Key Vault, os aplicativos não precisarão ser reimplantados porque eles coletarão a nova chave da Azure Key Vault automaticamente. Observe que você pode fazer com que o aplicativo Leia a chave sempre que precisar, ou pode armazená-la em cache na memória e se ela falhar ao usá-la, recuperar a chave novamente do Azure Key Vault.

O uso de Azure Key Vault também adiciona outro nível de segurança para suas chaves de armazenamento. Se você usar esse método, nunca terá a chave de armazenamento codificada em um arquivo de configuração, o que removerá essa avenida de alguém que está obtendo acesso às chaves sem permissão específica.

Outra vantagem de usar o Azure Key Vault é que você também pode controlar o acesso às suas chaves usando Azure Active Directory. Isso significa que você pode conceder acesso a alguns aplicativos que precisam recuperar as chaves de Azure Key Vault e saber que outros aplicativos não poderão acessar as chaves sem conceder a permissão especificamente.

> [!NOTE]
> A Microsoft recomenda usar apenas uma das chaves em todos os seus aplicativos ao mesmo tempo. Se você usar a chave 1 em alguns lugares e a chave 2 em outros, não poderá girar suas chaves sem que algum aplicativo perca o acesso.

#### <a name="resources"></a>Recursos

* [Gerir as definições da conta de armazenamento no portal do Azure](storage-account-manage.md)
* [Referência da API REST do Fornecedor de Recursos de Armazenamento do Azure](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Segurança do plano de dados
A segurança do plano de dados refere-se aos métodos usados para proteger os objetos de dados armazenados no armazenamento do Azure – os BLOBs, as filas, as tabelas e os arquivos. Vimos métodos para criptografar os dados e a segurança durante o trânsito dos dados, mas como você pode controlar o acesso aos objetos?

Você tem três opções para autorizar o acesso a objetos de dados no armazenamento do Azure, incluindo:

- Usando o Azure AD para autorizar o acesso a contêineres e filas. O Azure AD fornece vantagens sobre outras abordagens à autorização, incluindo a remoção da necessidade de armazenar segredos em seu código. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md). 
- Usando as chaves da conta de armazenamento para autorizar o acesso via chave compartilhada. A autorização via chave compartilhada requer o armazenamento de suas chaves de conta de armazenamento em seu aplicativo, portanto, a Microsoft recomenda usar o Azure AD, quando possível.
- Usar assinaturas de acesso compartilhado para conceder permissões controladas a objetos de dados específicos por um período específico.

Além disso, para o armazenamento de BLOBs, você pode permitir acesso público a seus BLOBs definindo o nível de acesso do contêiner que contém os blobs de acordo. Se você definir o acesso para um contêiner para BLOB ou contêiner, ele permitirá acesso de leitura público para os BLOBs nesse contêiner. Isso significa que qualquer pessoa com uma URL apontando para um blob nesse contêiner pode abri-lo em um navegador sem usar uma assinatura de acesso compartilhado ou ter as chaves da conta de armazenamento.

Além de limitar o acesso por meio de autorização, você também pode usar [firewalls e redes virtuais](storage-network-security.md) para limitar o acesso à conta de armazenamento com base nas regras de rede.  Essa abordagem permite que você negue o acesso ao tráfego de Internet público e conceda acesso somente a redes virtuais específicas do Azure ou intervalos de endereços IP de Internet públicos.

### <a name="storage-account-keys"></a>Chaves de Contas de Armazenamento
As chaves da conta de armazenamento são cadeias de caracteres de 512 bits criadas pelo Azure que, juntamente com o nome da conta de armazenamento, podem ser usadas para acessar os objetos de dados armazenados na conta de armazenamento.

Por exemplo, você pode ler BLOBs, gravar em filas, criar tabelas e modificar arquivos. Muitas dessas ações podem ser executadas por meio do portal do Azure ou usando um dos muitos aplicativos Gerenciador de Armazenamento. Você também pode escrever código para usar a API REST ou uma das bibliotecas de cliente de armazenamento para executar essas operações.

Conforme discutido na seção sobre a [segurança do plano de gerenciamento](#management-plane-security), o acesso às chaves de armazenamento para uma conta de armazenamento clássica pode ser concedido fornecendo acesso completo à assinatura do Azure. O acesso às chaves de armazenamento para uma conta de armazenamento usando o modelo de Azure Resource Manager pode ser controlado por meio do controle de acesso baseado em função (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como delegar acesso a objetos em sua conta usando assinaturas de acesso compartilhado e políticas de acesso armazenadas
Uma assinatura de acesso compartilhado é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI que permite delegar acesso a objetos de armazenamento e especificar restrições como as permissões e o intervalo de data/hora de acesso.

Você pode conceder acesso a BLOBs, contêineres, mensagens de fila, arquivos e tabelas. Com tabelas, você pode, na verdade, conceder permissão para acessar um intervalo de entidades na tabela especificando os intervalos de chaves de linha e partição aos quais você deseja que o usuário tenha acesso. Por exemplo, se você tiver dados armazenados com uma chave de partição de estado geográfico, poderá conceder a alguém acesso apenas aos dados da Califórnia.

Em outro exemplo, você pode dar a um aplicativo Web um token SAS que permite gravar entradas em uma fila e dar a um aplicativo de função de trabalho um token SAS para obter mensagens da fila e processá-las. Ou você pode dar a um cliente um token SAS que ele pode usar para carregar imagens em um contêiner no armazenamento de BLOBs e conceder a um aplicativo Web permissão para ler essas imagens. Em ambos os casos, há uma separação de preocupações – cada aplicativo pode receber apenas o acesso que eles precisam para executar sua tarefa. Isso é possível por meio do uso de assinaturas de acesso compartilhado.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Por que você deseja usar assinaturas de acesso compartilhado
Por que você desejaria usar uma SAS em vez de apenas fornecer sua chave de conta de armazenamento, o que é muito mais fácil? Desistir da sua chave de conta de armazenamento é como compartilhar as chaves do seu Reino de armazenamento. Ele concede acesso completo. Alguém pode usar suas chaves e carregar toda a biblioteca de músicas em sua conta de armazenamento. Eles também podem substituir seus arquivos por versões infectadas por vírus ou roubar seus dados. Desistir de acesso ilimitado à sua conta de armazenamento é algo que não deve ser pouco levado.

Com as assinaturas de acesso compartilhado, você pode dar a um cliente apenas as permissões necessárias por um período limitado. Por exemplo, se alguém estiver carregando um blob para sua conta, você poderá conceder a eles acesso de gravação por apenas tempo suficiente para carregar o blob (dependendo do tamanho do blob, é claro). E se você mudar de ideia, poderá revogar esse acesso.

Além disso, você pode especificar que as solicitações feitas usando uma SAS sejam restritas a determinado endereço IP ou intervalo de endereços IP externo ao Azure. Você também pode exigir que as solicitações sejam feitas usando um protocolo específico (HTTPS ou HTTP/HTTPS). Isso significa que se você quiser apenas permitir o tráfego HTTPS, poderá definir o protocolo necessário para somente HTTPS e o tráfego HTTP será bloqueado.

#### <a name="definition-of-a-shared-access-signature"></a>Definição de uma assinatura de acesso compartilhado
Uma assinatura de acesso compartilhado é um conjunto de parâmetros de consulta anexados à URL que aponta para o recurso

Isso fornece informações sobre o acesso permitido e o período de tempo para o qual o acesso é permitido. Aqui está um exemplo; esse URI fornece acesso de leitura a um blob por cinco minutos. Observe que os parâmetros de consulta SAS devem ser codificados em URL, como% 3A para dois-pontos (:) ou %20 para um espaço.

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

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Como a assinatura de acesso compartilhado é autorizada pelo serviço de armazenamento do Azure
Quando o serviço de armazenamento recebe a solicitação, ele usa os parâmetros de consulta de entrada e cria uma assinatura usando o mesmo método que o programa de chamada. Em seguida, ele compara as duas assinaturas. Se eles concordarem, o serviço de armazenamento poderá verificar a versão do serviço de armazenamento para certificar-se de que ele é válido, verificar se a data e a hora atuais estão dentro da janela especificada, certificar-se de que o acesso solicitado corresponde à solicitação feita, etc.

Por exemplo, com nossa URL acima, se a URL estivesse apontando para um arquivo em vez de um blob, essa solicitação falharia porque ela especifica que a assinatura de acesso compartilhado é para um blob. Se o comando REST que está sendo chamado for atualizar um blob, ele falharia porque a assinatura de acesso compartilhado especifica que apenas o acesso de leitura é permitido.

#### <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso compartilhado
* Uma SAS de nível de serviço pode ser usada para acessar recursos específicos em uma conta de armazenamento. Alguns exemplos disso são recuperar uma lista de BLOBs em um contêiner, baixar um blob, atualizar uma entidade em uma tabela, adicionar mensagens a uma fila ou carregar um arquivo em um compartilhamento de arquivos.
* Uma SAS de nível de conta pode ser usada para acessar qualquer coisa à qual uma SAS de nível de serviço pode ser usada. Além disso, ele pode dar opções a recursos que não são permitidos com uma SAS de nível de serviço, como a capacidade de criar contêineres, tabelas, filas e compartilhamentos de arquivos. Você também pode especificar o acesso a vários serviços ao mesmo tempo. Por exemplo, você pode fornecer a alguém acesso a BLOBs e arquivos em sua conta de armazenamento.

#### <a name="creating-a-sas-uri"></a>Criando um URI de SAS
1. Você pode criar um URI sob demanda, definindo todos os parâmetros de consulta a cada vez.

   Essa abordagem é flexível, mas se você tiver um conjunto lógico de parâmetros que são semelhantes a cada vez, usar uma política de acesso armazenada é uma ideia melhor.
2. Você pode criar uma política de acesso armazenada para um contêiner inteiro, um compartilhamento de arquivos, uma tabela ou uma fila. Em seguida, você pode usar isso como a base para os URIs de SAS que você criar. As permissões baseadas em políticas de acesso armazenadas podem ser facilmente revogadas. Você pode ter até cinco políticas definidas em cada contêiner, fila, tabela ou compartilhamento de arquivos.

   Por exemplo, se você tiver muitas pessoas lendo os BLOBs em um contêiner específico, poderá criar uma política de acesso armazenado que diga "conceder acesso de leitura" e quaisquer outras configurações que serão as mesmas todas as vezes. Em seguida, você pode criar um URI de SAS usando as configurações da política de acesso armazenada e especificando a data/hora de expiração. A vantagem disso é que você não precisa especificar todos os parâmetros de consulta todas as vezes.

#### <a name="revocation"></a>Revogação
Suponha que a SAS tenha sido comprometida ou que você queira alterá-la devido à segurança corporativa ou aos requisitos de conformidade regulatória. Como você revoga o acesso a um recurso usando essa SAS? Depende de como você criou o URI de SAS.

Se você estiver usando URIs ad hoc, terá três opções. Você pode emitir tokens SAS com políticas de expiração curta e aguardar a SAS expirar. Você pode renomear ou excluir o recurso (supondo que o token tenha sido definido como um único objeto). Você pode alterar as chaves da conta de armazenamento. Essa última opção pode ter um impacto significativo, dependendo de quantos serviços estão usando essa conta de armazenamento, e provavelmente não é algo que você queira fazer sem algum planejamento.

Se você estiver usando uma SAS derivada de uma política de acesso armazenada, poderá remover o acesso revogando a política de acesso armazenada – você pode apenas alterá-la para que ela já tenha expirado ou você pode removê-la completamente. Isso entra em vigor imediatamente e invalida todas as SAS criadas usando essa política de acesso armazenada. Atualizar ou remover a política de acesso armazenada pode afetar as pessoas que acessam o contêiner específico, o compartilhamento de arquivos, a tabela ou a fila via SAS, mas se os clientes forem gravados para que solicitem uma nova SAS quando a antiga se tornar inválida, isso funcionará bem.

Como o uso de uma SAS derivada de uma política de acesso armazenada oferece a capacidade de revogar essa SAS imediatamente, é a prática recomendada sempre usar políticas de acesso armazenado quando possível.

#### <a name="resources"></a>Recursos
Para obter informações mais detalhadas sobre como usar assinaturas de acesso compartilhado e políticas de acesso armazenado, conclua com exemplos, consulte os seguintes artigos:

* Estes são os artigos de referência.

  * [SAS do serviço](https://msdn.microsoft.com/library/dn140256.aspx)

    Este artigo fornece exemplos de como usar uma SAS de nível de serviço com BLOBs, mensagens de fila, intervalos de tabelas e arquivos.
  * [Construindo uma SAS de serviço](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Construindo uma SAS de conta](https://msdn.microsoft.com/library/mt584140.aspx)

* Autenticação

  * [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Introdução tutorial sobre assinaturas de acesso compartilhado

  * [Tutorial de Introdução SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Criptografia em trânsito
### <a name="transport-level-encryption--using-https"></a>Criptografia no nível de transporte – usando HTTPS
Outra etapa que deve ser tomada para garantir a segurança dos dados do armazenamento do Azure é criptografar os dados entre o cliente e o armazenamento do Azure. A primeira recomendação é sempre usar o protocolo [https](https://en.wikipedia.org/wiki/HTTPS) , o que garante uma comunicação segura pela Internet pública.

Para ter um canal de comunicação seguro, você sempre deve usar HTTPS ao chamar as APIs REST ou acessar objetos no armazenamento. Além disso, as **assinaturas de acesso compartilhado**, que podem ser usadas para delegar acesso aos objetos de armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser usado ao usar assinaturas de acesso compartilhado, garantindo que qualquer pessoa envie links com TOKENs SAS usará o protocolo apropriado.

Você pode impor o uso de HTTPS ao chamar as APIs REST para acessar objetos em contas de armazenamento habilitando a [transferência segura necessária](../storage-require-secure-transfer.md) para a conta de armazenamento. As conexões que usam HTTP serão recusadas quando isso estiver habilitado.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Usando a criptografia durante o trânsito com compartilhamentos de arquivos do Azure
Os [arquivos do Azure](../files/storage-files-introduction.md) dão suporte à criptografia via SMB 3,0 e com https ao usar a API REST do arquivo. Ao montar fora da região do Azure, o compartilhamento de arquivos do Azure está localizado no, como no local ou em outra região do Azure, o SMB 3,0 com criptografia é sempre necessário. O SMB 2,1 não dá suporte à criptografia, portanto, por padrão, as conexões só são permitidas na mesma região no Azure, mas o SMB 3,0 com criptografia pode ser imposto, [exigindo a transferência segura](../storage-require-secure-transfer.md) para a conta de armazenamento.

O SMB 3,0 com criptografia está disponível em [todos os sistemas operacionais Windows e Windows Server com suporte](../files/storage-how-to-use-files-windows.md) , exceto no Windows 7 e no windows Server 2008 R2, que dão suporte apenas ao SMB 2,1. O SMB 3,0 também tem suporte no [MacOS](../files/storage-how-to-use-files-mac.md) e em distribuições do [Linux](../files/storage-how-to-use-files-linux.md) usando o kernel do Linux 4,11 e superior. O suporte de criptografia para SMB 3,0 também foi reportado para versões mais antigas do kernel do Linux por várias distribuições do Linux, consulte [noções básicas sobre os requisitos do cliente SMB](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Usando a criptografia do lado do cliente para proteger os dados que você envia para o armazenamento
Outra opção que ajuda a garantir que seus dados sejam protegidos durante a transferência entre um aplicativo cliente e o armazenamento é a criptografia do lado do cliente. Os dados são criptografados antes de serem transferidos para o armazenamento do Azure. Ao recuperar os dados do armazenamento do Azure, os dados são descriptografados após serem recebidos no lado do cliente. Embora os dados sejam criptografados pela conexão, recomendamos que você também use HTTPS, pois ele tem verificações de integridade de dados criadas para ajudar a atenuar os erros de rede que afetam a integridade dos dados.

A criptografia do lado do cliente também é um método para criptografar seus dados em repouso, pois os dados são armazenados em seu formato criptografado. Falaremos sobre isso mais detalhadamente na seção sobre [criptografia em repouso](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Criptografia em repouso
Há três recursos do Azure que fornecem criptografia em repouso. Azure Disk Encryption é usado para criptografar o sistema operacional e os discos de dados em máquinas virtuais IaaS. A criptografia do lado do cliente e a SSE são usadas para criptografar dados no armazenamento do Azure. 

Embora você possa usar a criptografia do lado do cliente para criptografar os dados em trânsito (que também são armazenados em seu formato criptografado no armazenamento), você pode preferir usar HTTPS durante a transferência e ter alguma maneira de os dados serem criptografados automaticamente quando armazenados. Há duas maneiras de fazer isso – Azure Disk Encryption e SSE. Uma é usada para criptografar diretamente os dados no sistema operacional e nos discos de dados usados pelas VMs, e a outra é usada para criptografar os dados gravados no armazenamento de BLOBs do Azure.

### <a name="storage-service-encryption-sse"></a>Criptografia do Serviço de Armazenamento (SSE)

A SSE está habilitada para todas as contas de armazenamento e não pode ser desabilitada. A SSE criptografa seus dados automaticamente ao gravá-los no armazenamento do Azure. Quando você lê dados do armazenamento do Azure, eles são descriptografados pelo armazenamento do Azure antes de serem retornados. A SSE permite que você proteja seus dados sem precisar modificar código nem adicionar código a nenhum aplicativo.

Você pode usar chaves gerenciadas pela Microsoft ou suas próprias chaves personalizadas. A Microsoft gera chaves gerenciadas e manipula seu armazenamento seguro, bem como sua rotação regular, conforme definido pela política interna da Microsoft. Para obter mais informações sobre como usar chaves personalizadas, consulte [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

O SSE encripta automaticamente dados em todos os escalões de desempenho (Standard e Premium), todos os modelos de implementação (Azure Resource Manager e Clássico) e todos os serviços de Armazenamento do Azure (Blob, Fila, Tabela e Ficheiro). 

### <a name="client-side-encryption"></a>Criptografia do lado do cliente
Mencionamos a criptografia do lado do cliente ao discutir a criptografia dos dados em trânsito. Esse recurso permite que você criptografe programaticamente seus dados em um aplicativo cliente antes de enviá-los pela conexão para serem gravados no armazenamento do Azure e descriptografe os dados programaticamente depois de recuperá-los do armazenamento do Azure.

Isso fornece criptografia em trânsito, mas também fornece o recurso de criptografia em repouso. Embora os dados sejam criptografados em trânsito, ainda é recomendável usar HTTPS para aproveitar as verificações de integridade de dados internas que ajudam a atenuar erros de rede que afetam a integridade dos dados.

Um exemplo de onde você pode usar isso é se você tiver um aplicativo Web que armazena BLOBs e recupera BLOBs e deseja que o aplicativo e os dados sejam o mais seguros possível. Nesse caso, você usaria a criptografia do lado do cliente. O tráfego entre o cliente e o serviço blob do Azure contém o recurso criptografado, e ninguém pode interpretar os dados em trânsito e reconstitui-los em seus BLOBs privados.

A criptografia do lado do cliente é incorporada às bibliotecas de cliente de armazenamento do Java e do .NET, que, por sua vez, usam as APIs de Azure Key Vault, facilitando a implementação. O processo de criptografar e descriptografar os dados usa a técnica de envelope e armazena os metadados usados pela criptografia em cada objeto de armazenamento. Por exemplo, para BLOBs, ele o armazena nos metadados de BLOB, enquanto para filas, adiciona-o a cada mensagem da fila.

Para a criptografia em si, você pode gerar e gerenciar suas próprias chaves de criptografia. Você também pode usar as chaves geradas pela biblioteca de cliente de armazenamento do Azure ou pode fazer com que o Azure Key Vault gere as chaves. Você pode armazenar suas chaves de criptografia em seu armazenamento de chaves local ou pode armazená-las em um Azure Key Vault. Azure Key Vault permite que você conceda acesso aos segredos em Azure Key Vault a usuários específicos usando Azure Active Directory. Isso significa que não apenas qualquer pessoa pode ler o Azure Key Vault e recuperar as chaves que você está usando para criptografia do lado do cliente.

#### <a name="resources"></a>Recursos
* [Criptografar e descriptografar BLOBs no Armazenamento do Microsoft Azure usando Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Este artigo mostra como usar a criptografia do lado do cliente com o Azure Key Vault, incluindo como criar o KEK e armazená-lo no cofre usando o PowerShell.
* [Criptografia do lado do cliente e Azure Key Vault para Armazenamento do Microsoft Azure](../storage-client-side-encryption.md)

  Este artigo fornece uma explicação da criptografia do lado do cliente e fornece exemplos de como usar a biblioteca de cliente de armazenamento para criptografar e descriptografar recursos dos quatro serviços de armazenamento. Ele também fala sobre Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Usando Azure Disk Encryption para criptografar discos usados por suas máquinas virtuais
Azure Disk Encryption permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS. Para o Windows, as unidades são criptografadas usando a tecnologia de criptografia BitLocker padrão do setor. Para o Linux, os discos são criptografados usando a tecnologia DM-cript. Isso é integrado com o Azure Key Vault para permitir que você controle e gerencie as chaves de criptografia de disco.

A solução dá suporte aos seguintes cenários para VMs de IaaS quando elas estão habilitadas no Microsoft Azure:

* Integração com o Azure Key Vault
* VMs da camada Standard: [VMs IaaS da série A, D, DS, G, GS e assim por diante](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Habilitando a criptografia em VMs IaaS Windows e Linux
* Desabilitando a criptografia no sistema operacional e nas unidades de dados para VMs IaaS do Windows
* Desabilitando a criptografia em unidades de dados para VMs IaaS do Linux
* Habilitando a criptografia em VMs IaaS que executam o sistema operacional Windows Client
* Habilitando a criptografia em volumes com caminhos de montagem
* Habilitando a criptografia em VMs Linux configuradas com a distribuição de disco (RAID) usando o mdadm
* Habilitando a criptografia em VMs do Linux usando o LVM para discos de dados
* Habilitando a criptografia em VMs do Windows configuradas usando espaços de armazenamento
* Há suporte para todas as regiões públicas do Azure

A solução não dá suporte aos seguintes cenários, recursos e tecnologia na versão:

* VMs IaaS de camada básica
* Desabilitando a criptografia em uma unidade do sistema operacional para VMs IaaS do Linux
* VMs de IaaS criadas usando o método de criação de VM clássico
* Integração com o serviço de gerenciamento de chaves local
* Arquivos do Azure (sistema de arquivos compartilhados), NFS (sistema de arquivos de rede), volumes dinâmicos e VMs do Windows configurados com sistemas RAID baseados em software


> [!NOTE]
> Atualmente, há suporte para a criptografia de disco do sistema operacional Linux nas seguintes distribuições Linux: RHEL 7,2, CentOS 7.2 n e Ubuntu 16, 4.
>
>

Esse recurso garante que todos os dados em seus discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.

#### <a name="resources"></a>Recursos
* [Azure Disk Encryption para VMs IaaS Windows e Linux](../../security/fundamentals/encryption-overview.md)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparação entre Azure Disk Encryption, SSE e criptografia do lado do cliente

#### <a name="iaas-vms-and-their-vhd-files"></a>VMs IaaS e seus arquivos VHD

Para discos de dados usados por VMs IaaS, o Azure Disk Encryption é recomendado. Se você criar uma VM com discos não gerenciados usando uma imagem do Azure Marketplace, o Azure executará uma [cópia superficial](https://en.wikipedia.org/wiki/Object_copying) da imagem em sua conta de armazenamento no armazenamento do Azure e ele não será criptografado mesmo que você tenha habilitado a SSE. Depois de criar a VM e começar a atualizar a imagem, a SSE começará a criptografar os dados. Por esse motivo, é melhor usar Azure Disk Encryption em VMs com discos não gerenciados criados a partir de imagens no Azure Marketplace se você quiser que elas sejam totalmente criptografadas. Se você criar uma VM com Managed Disks, a SSE criptografará todos os dados por padrão usando chaves gerenciadas pela plataforma. 

Se você colocar uma VM previamente criptografada no Azure do local, poderá carregar as chaves de criptografia para Azure Key Vault e continuar usando a criptografia para essa VM que você estava usando no local. Azure Disk Encryption está habilitado para lidar com esse cenário.

Se você tiver um VHD não criptografado do local, poderá carregá-lo na galeria como uma imagem personalizada e provisionar uma VM a partir dela. Se você fizer isso usando os modelos do Resource Manager, poderá pedir que ele ative Azure Disk Encryption ao inicializar a VM.

Ao adicionar um disco de dados e montá-lo na VM, você pode ativar Azure Disk Encryption nesse disco de dados. Ele criptografará primeiro o disco de dados localmente e, em seguida, a camada de modelo de implantação clássica fará uma gravação lenta em relação ao armazenamento para que o conteúdo de armazenamento seja criptografado.

#### <a name="client-side-encryption"></a>Encriptação do lado do cliente
A criptografia do lado do cliente é o método mais seguro de criptografar seus dados, pois ele criptografa os dados antes do trânsito.  No entanto, ele requer que você adicione código aos seus aplicativos usando o armazenamento, que talvez você não queira fazer. Nesses casos, você pode usar HTTPS para proteger seus dados em trânsito. Depois que os dados atingem o armazenamento do Azure, eles são criptografados pelo SSE.

Com a criptografia do lado do cliente, você pode criptografar entidades de tabela, mensagens de fila e blobs. 

A criptografia do lado do cliente é totalmente gerenciada pelo aplicativo. Essa é a abordagem mais segura, mas exige que você faça alterações programáticas em seu aplicativo e coloque os processos de gerenciamento de chaves em vigor. Você usaria isso quando quiser a segurança extra durante o trânsito e quiser que os dados armazenados sejam criptografados.

A criptografia do lado do cliente é mais carga no cliente e você precisa considerar isso em seus planos de escalabilidade, especialmente se você estiver Criptografando e transferindo uma grande quantidade de dados.

#### <a name="storage-service-encryption-sse"></a>Criptografia do Serviço de Armazenamento (SSE)

A SSE é gerenciada pelo armazenamento do Azure. A SSE não fornece a segurança dos dados em trânsito, mas criptografa os dados conforme eles são gravados no armazenamento do Azure. O SSE não afeta o desempenho do Armazenamento do Azure.

Você pode criptografar qualquer tipo de dados da conta de armazenamento usando o SSE (BLOBs de blocos, blobs de acréscimo, blobs de página, dados de tabela, dados de fila e arquivos).

Se você tiver um arquivo ou uma biblioteca de arquivos VHD que usa como base para a criação de novas máquinas virtuais, poderá criar uma nova conta de armazenamento e, em seguida, carregar os arquivos VHD nessa conta. Esses arquivos VHD serão criptografados pelo armazenamento do Azure.

Se você tiver Azure Disk Encryption habilitado para os discos em uma VM, todos os dados gravados recentemente serão criptografados por SSE e por Azure Disk Encryption.

## <a name="storage-analytics"></a>Análise de Armazenamento
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Usando Análise de Armazenamento para monitorar o tipo de autorização
Para cada conta de armazenamento, você pode habilitar Análise de Armazenamento do Azure para executar logs e armazenar dados de métricas. Essa é uma ótima ferramenta para usar quando você quiser verificar as métricas de desempenho de uma conta de armazenamento ou precisar solucionar problemas de uma conta de armazenamento, pois você está tendo problemas de desempenho.

Outra parte dos dados que você pode ver nos logs de análise de armazenamento é o método de autenticação usado por alguém quando acessa o armazenamento. Por exemplo, com o armazenamento de BLOBs, você pode ver se eles usaram uma assinatura de acesso compartilhado ou as chaves da conta de armazenamento, ou se o blob acessado era público.

Isso pode ser útil se você estiver protegendo rigidamente o acesso ao armazenamento. Por exemplo, no armazenamento de BLOBs, você pode definir todos os contêineres para privado e implementar o uso de um serviço SAS em todos os seus aplicativos. Em seguida, você pode verificar os logs regularmente para ver se os BLOBs são acessados usando as chaves da conta de armazenamento, o que pode indicar uma violação de segurança ou se os BLOBs são públicos, mas não deveriam estar.

#### <a name="what-do-the-logs-look-like"></a>Como é a aparência dos logs?
Depois de habilitar as métricas da conta de armazenamento e o registro em log por meio do portal do Azure, os dados de análise começarão a se acumular rapidamente. O registro em log e as métricas para cada serviço são separados; o registro em log só é gravado quando há atividade nessa conta de armazenamento, enquanto as métricas serão registradas a cada minuto, a cada hora ou todos os dias, dependendo de como você a configura.

Os logs são armazenados em blobs de blocos em um contêiner chamado $logs na conta de armazenamento. Esse contêiner é criado automaticamente quando Análise de Armazenamento está habilitado. Depois que esse contêiner é criado, não é possível excluí-lo, embora você possa excluir seu conteúdo.

No contêiner $logs, há uma pasta para cada serviço e há subpastas para o ano/mês/dia/hora. Em hora, os logs são numerados. Essa será a aparência da estrutura de diretório:

![Exibição de arquivos de log](./media/storage-security-guide/image1.png)

Cada solicitação para o armazenamento do Azure é registrada. Aqui está um instantâneo de um arquivo de log, mostrando os primeiros campos.

![Instantâneo de um arquivo de log](./media/storage-security-guide/image2.png)

Você pode ver que pode usar os logs para rastrear qualquer tipo de chamada para uma conta de armazenamento.

#### <a name="what-are-all-of-those-fields-for"></a>Para que são todos esses campos?
Há um artigo listado nos recursos abaixo que fornece a lista de muitos campos nos logs e para que eles são usados. Aqui está a lista de campos na ordem:

![Instantâneo de campos em um arquivo de log](./media/storage-security-guide/image3.png)

Estamos interessados nas entradas para getBlob e como elas são autorizadas, portanto, precisamos procurar entradas com o tipo de operação "Get-blob" e verificar a solicitação-status (quarta </sup> coluna) e o tipo de autorização (oitava </sup> coluna).

Por exemplo, nas primeiras linhas da listagem acima, a solicitação-status é "êxito" e o tipo de autorização é "autenticado". Isso significa que a solicitação foi autorizada usando a chave da conta de armazenamento.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Como o acesso aos meus BLOBs está sendo autorizado?
Temos três casos em que estamos interessados.

1. O blob é público e é acessado usando uma URL sem uma assinatura de acesso compartilhado. Nesse caso, a solicitação-status é "AnonymousSuccess" e o tipo de autorização é "Anonymous".

   1.0; 2015-11-17T02:01:29.0488963 Z; GetBlob **AnonymousSuccess**; 200; 124; 37; **anônimo**;; mystorage...
2. O blob é privado e foi usado com uma assinatura de acesso compartilhado. Nesse caso, a solicitação-status é "SASSuccess" e o tipo de autorização é "SAS".

   1.0; 2015-11-16T18:30:05.6556115 Z; GetBlob **SASSuccess**; 200; 416; 64; **SAS**;; mystorage...
3. O blob é privado e a chave de armazenamento foi usada para acessá-lo. Nesse caso, a solicitação-status é "**êxito**" e o tipo de autorização é "**autenticado**".

   1.0; 2015-11-16T18:32:24.3174537 Z; GetBlob **Sucesso**; 206; 59; 22; **autenticado**; mystorage...

Você pode usar o Microsoft Message Analyzer para exibir e analisar esses logs. Ele inclui recursos de pesquisa e de filtro. Por exemplo, talvez você queira procurar instâncias de getBlob para ver se o uso é o esperado, ou seja, para garantir que alguém não esteja acessando a conta de armazenamento de forma inadequada.

#### <a name="resources"></a>Recursos
* [Análise de Armazenamento](../storage-analytics.md)

  Este artigo é uma visão geral da análise de armazenamento e como habilitá-las.
* [Formato de log de Análise de Armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Este artigo ilustra o formato de log de Análise de Armazenamento e detalha os campos disponíveis nele, incluindo autenticação-tipo, que indica o tipo de autenticação usado para a solicitação.
* [Monitorar uma conta de armazenamento no portal do Azure](../storage-monitor-storage-account.md)

  Este artigo mostra como configurar o monitoramento de métricas e registro em log para uma conta de armazenamento.
* [Solução de problemas de ponta a ponta usando métricas de armazenamento do Azure e registro em log, AzCopy e analisador de mensagem](../storage-e2e-troubleshooting.md)

  Este artigo aborda a solução de problemas usando o Análise de Armazenamento e mostra como usar o analisador de mensagem da Microsoft.
* [Guia de operação do Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)

  Este artigo é a referência para o analisador de mensagem da Microsoft e inclui links para um tutorial, início rápido e Resumo de recursos.

## <a name="cross-origin-resource-sharing-cors"></a>Partilha de Recursos Transversais à Origem (CORS)
### <a name="cross-domain-access-of-resources"></a>Acesso entre domínios de recursos
Quando um navegador da Web em execução em um domínio faz uma solicitação HTTP para um recurso de um domínio diferente, isso é chamado de solicitação HTTP entre origens. Por exemplo, uma página HTML fornecida pelo contoso.com faz uma solicitação para um JPEG hospedado em fabrikam.blob.core.windows.net. Por motivos de segurança, os navegadores restringem solicitações HTTP entre origens iniciadas de dentro de scripts, como JavaScript. Isso significa que, quando algum código JavaScript em uma página da Web em contoso.com solicita que o JPEG no fabrikam.blob.core.windows.net, o navegador não permitirá a solicitação.

O que isso tem a ver com o armazenamento do Azure? Bem, se você estiver armazenando ativos estáticos como arquivos de dados JSON ou XML no armazenamento de BLOBs usando uma conta de armazenamento chamada Fabrikam, o domínio para os ativos será fabrikam.blob.core.windows.net e o aplicativo Web contoso.com não poderá acessá-los usando JavaScript porque os domínios são diferentes. Isso também será verdadeiro se você estiver tentando chamar um dos serviços de armazenamento do Azure, como o armazenamento de tabelas, que retornam dados JSON a serem processados pelo cliente JavaScript.

#### <a name="possible-solutions"></a>Soluções possíveis
Uma maneira de resolver isso é atribuir um domínio personalizado como "storage.contoso.com" a fabrikam.blob.core.windows.net. O problema é que você só pode atribuir esse domínio personalizado a uma conta de armazenamento. E se os ativos estiverem armazenados em várias contas de armazenamento?

Outra maneira de resolver isso é fazer com que o aplicativo Web atue como um proxy para as chamadas de armazenamento. Isso significa que, se você estiver carregando um arquivo no armazenamento de BLOBs, o aplicativo Web o gravará localmente e, em seguida, o copiará para o armazenamento de BLOBs, ou ele lerá tudo na memória e, em seguida, o gravará no armazenamento de BLOBs. Como alternativa, você poderia escrever um aplicativo Web dedicado (como uma API Web) que carregue os arquivos localmente e os grave no armazenamento de BLOBs. De qualquer forma, você precisa considerar essa função ao determinar as necessidades de escalabilidade.

#### <a name="how-can-cors-help"></a>Como a CORS pode ajudar?
O armazenamento do Azure permite habilitar o CORS – compartilhamento de recursos entre origens. Para cada conta de armazenamento, você pode especificar domínios que podem acessar os recursos nessa conta de armazenamento. Por exemplo, em nosso caso descrito acima, podemos habilitar o CORS na conta de armazenamento fabrikam.blob.core.windows.net e configurá-lo para permitir o acesso ao contoso.com. Em seguida, o aplicativo Web contoso.com pode acessar diretamente os recursos no fabrikam.blob.core.windows.net.

Uma coisa a ser observada é que o CORS permite o acesso, mas não fornece autenticação, que é necessária para todo o acesso não público de recursos de armazenamento. Isso significa que você só poderá acessar BLOBs se eles forem públicos ou se você incluir uma assinatura de acesso compartilhado fornecendo a permissão apropriada. Tabelas, filas e arquivos não têm acesso público e exigem uma SAS.

Por padrão, o CORS está desabilitado em todos os serviços. Você pode habilitar o CORS usando a API REST ou a biblioteca de cliente de armazenamento para chamar um dos métodos para definir as políticas de serviço. Ao fazer isso, você inclui uma regra CORS, que está em XML. Aqui está um exemplo de uma regra de CORS que foi definida usando a operação definir propriedades do serviço para o serviço blob para uma conta de armazenamento. Você pode executar essa operação usando a biblioteca de cliente de armazenamento ou as APIs REST para o armazenamento do Azure.

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

Veja o que cada linha significa:

* **AllowedOrigins** Isso informa quais domínios não correspondentes podem solicitar e receber dados do serviço de armazenamento. Isso diz que tanto contoso.com quanto fabrikam.com podem solicitar dados do armazenamento de BLOBs para uma conta de armazenamento específica. Você também pode definir isso como um curinga (\*) para permitir que todos os domínios acessem solicitações.
* **AllowedMethods** Esta é a lista de métodos (verbos de solicitação HTTP) que podem ser usados ao fazer a solicitação. Neste exemplo, somente PUT e GET são permitidos. Você pode definir isso como um curinga (\*) para permitir que todos os métodos sejam usados.
* **AllowedHeaders** Esses são os cabeçalhos de solicitação que o domínio de origem pode especificar ao fazer a solicitação. Neste exemplo, todos os cabeçalhos de metadados que começam com x-MS-meta-data, x-MS-meta-Target e x-MS-meta-ABC são permitidos. O caractere curinga (\*) indica que qualquer cabeçalho que comece com o prefixo especificado é permitido.
* **ExposedHeaders** Isso informa quais cabeçalhos de resposta devem ser expostos pelo navegador para o emissor da solicitação. Neste exemplo, qualquer cabeçalho que começa com "x-MS-meta-" será exposto.
* **MaxAgeInSeconds** Essa é a quantidade máxima de tempo que um navegador armazenará em cache a solicitação de simulação de opções. (Para obter mais informações sobre a solicitação de simulação, verifique o primeiro artigo abaixo.)

#### <a name="resources"></a>Recursos
Para obter mais informações sobre o CORS e como habilitá-lo, Confira esses recursos.

* [Suporte a CORS (compartilhamento de recursos entre origens) para os serviços de armazenamento do Azure no Azure.com](../storage-cors-support.md)

  Este artigo fornece uma visão geral do CORS e como definir as regras para os diferentes serviços de armazenamento.
* [Suporte a CORS (compartilhamento de recursos entre origens) para os serviços de armazenamento do Azure no MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Esta é a documentação de referência para suporte a CORS para os serviços de armazenamento do Azure. Isso tem links para artigos que se aplicam a cada serviço de armazenamento e mostra um exemplo e explica cada elemento no arquivo CORS.
* [Armazenamento do Microsoft Azure: Apresentando o CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Este é um link para o artigo inicial do blog anunciando CORS e mostrando como usá-lo.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Perguntas frequentes sobre a segurança de armazenamento do Azure
1. **Como posso verificar a integridade dos BLOBs que estou transferindo para dentro ou para fora do armazenamento do Azure se não for possível usar o protocolo HTTPS?**

   Se por algum motivo você precisar usar HTTP em vez de HTTPS e estiver trabalhando com blobs de bloco, poderá usar a verificação MD5 para ajudar a verificar a integridade dos BLOBs que estão sendo transferidos. Isso ajudará na proteção contra erros de camada de rede/transporte, mas não necessariamente com ataques intermediários.

   Se você puder usar HTTPS, que fornece segurança em nível de transporte, o uso da verificação MD5 será redundante e desnecessário.

   Para obter mais informações, consulte a [visão geral do Azure Blob MD5](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **E quanto à conformidade com FIPS para o governo dos EUA?**

   O Estados Unidos padrão FIPS (FIPS) define os algoritmos de criptografia aprovados para uso pelos sistemas de computador do governo federal dos EUA para a proteção de dados confidenciais. Habilitar o modo FIPS em um Windows Server ou desktop informa ao sistema operacional que apenas algoritmos criptográficos validados por FIPS devem ser usados. Se um aplicativo usar algoritmos não compatíveis, os aplicativos serão interrompidos. With.NET Framework versões 4.5.2 ou superior, o aplicativo alterna automaticamente os algoritmos de criptografia para usar algoritmos compatíveis com FIPS quando o computador está no modo FIPS.

   A Microsoft deixa que cada cliente decida se deseja habilitar o modo FIPS. Acreditamos que não há nenhum motivo convincente para os clientes que não estão sujeitos a regulamentos governamentais para habilitar o modo FIPS por padrão.

### <a name="resources"></a>Recursos
* [Por que não estamos mais recomendando o "modo FIPS"](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Este artigo de blog fornece uma visão geral do FIPS e explica por que eles não habilitam o modo FIPS por padrão.
* [Validação do FIPS 140](https://technet.microsoft.com/library/cc750357.aspx)

  Este artigo fornece informações sobre como os produtos e módulos criptográficos da Microsoft estão em conformidade com o padrão FIPS para o governo federal dos EUA.
* ["Criptografia do sistema: usar algoritmos compatíveis com FIPS para criptografia, hash e assinatura" efeitos de configurações de segurança no Windows XP e em versões posteriores do Windows](https://support.microsoft.com/kb/811833)

  Este artigo fala sobre o uso do modo FIPS em computadores mais antigos do Windows.
