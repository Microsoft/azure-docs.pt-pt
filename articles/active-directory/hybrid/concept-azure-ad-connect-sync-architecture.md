---
title: 'Sincronização do Azure AD Connect: Compreendendo a arquitetura - Azure'
description: Este tópico descreve a arquitetura de sincronização do Azure AD Connect e explica os termos utilizados.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fac0f9143918d3f273812e53abfb88d6a56f7a71
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138592"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronização do Azure AD Connect: Compreender a arquitetura
Este tópico abrange a arquitetura básica para sincronização do Azure AD Connect. Em muitos aspectos, ele é semelhante a seus antecessores, o MIIS 2003, o ILM 2007 e o FIM 2010. Sincronização do Azure AD Connect é a evolução dessas tecnologias. Se estiver familiarizado com qualquer uma dessas tecnologias anteriores, o conteúdo deste tópico será familiar para também. Se estiver familiarizado com a sincronização, em seguida, este tópico é para. No entanto não é um requisito para saber os detalhes deste tópico para ter êxito na criação de personalizações para sincronização do Azure AD Connect (chamado de motor de sincronização neste tópico).

## <a name="architecture"></a>Arquitetura
O motor de sincronização cria uma vista integrada dos objetos que estão armazenados em várias fontes de dados ligados e gere informações de identidade dessas origens de dados. Esta vista integrada é determinada pelas informações de identidade obtidas a partir de origens de dados ligadas e um conjunto de regras que determinam como processar essas informações.

### <a name="connected-data-sources-and-connectors"></a>Origens de dados ligadas e conectores
O motor de sincronização processa informações de identidade de repositórios de dados diferentes, tais como o Active Directory ou uma base de dados do SQL Server. Cada repositório de dados que organiza os seus dados num formato semelhante a base de dados e que fornece métodos de acesso a dados padrão é um candidato potenciais para a origem de dados para o motor de sincronização. Os repositórios de dados que estão sincronizados com o motor de sincronização são chamados **ligado a origens de dados** ou **ligado diretórios** (CD).

O motor de sincronização encapsula a interação com uma origem de dados ligada dentro de um módulo chamado um **conector**. Cada tipo de origem de dados ligada tem um conector específico. O conector traduz-se uma operação necessária no formato que compreende a origem de dados ligada.

Conectores de fazer chamadas à API para trocar informações de identidade (leitura e escrita) com uma origem de dados ligada. Também é possível adicionar um conector personalizado com o framework de conectividade extensível. A ilustração seguinte mostra como um conector liga uma origem de dados ligada para o motor de sincronização.

![Arch1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Dados flua em ambas as direções, mas ele não pode fluir em ambas as direções em simultâneo. Em outras palavras, um conector pode ser configurado para permitir que os dados para o fluxo de origem de dados ligada para o motor de sincronização ou de motor de sincronização para a origem de dados ligada, mas apenas uma dessas operações pode ocorrer a qualquer momento para um objeto e o atributo. A direção pode ser diferente para diferentes objetos e atributos diferentes.

Para configurar um conector, especifique os tipos de objeto que pretende sincronizar. Especificar os tipos de objeto define o âmbito dos objetos que estão incluídos no processo de sincronização. A próxima etapa é selecionar os atributos a sincronizar, que é conhecido como uma lista de inclusão de atributo. Estas definições podem ser alteradas sempre em resposta a alterações para as regras de negócio. Quando utiliza o Assistente de instalação do Azure AD Connect, estas definições são configuradas para si.

Para exportar objetos para uma origem de dados ligada, a lista de inclusão de atributo tem de incluir, pelo menos, os atributos mínimo necessários para criar um tipo de objeto específico numa origem de dados ligada. Por exemplo, o **sAMAccountName** atributo tem de ser incluído na lista de inclusão de atributos para exportar um objeto de usuário para o Active Directory, porque todos os objetos de utilizador no Active Directory tem de ter um **sAMAccountName** atributo definido. Novamente, o Assistente de instalação faz esta configuração para.

Se a origem de dados ligada utiliza componentes estruturas, como as partições ou contentores para organizar os objetos, pode limitar as áreas da origem de dados ligadas que são utilizadas para uma determinada solução.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estrutura interna do espaço de nomes do motor de sincronização
O espaço de nomes do motor de sincronização completa é constituído por dois espaços de nomes que armazenam as informações de identidade. Os dois espaços de nomes são:

* O espaço conector (CS)
* O metaverse (MV)

O **espaço conector** é uma área de transição que contém as representações dos objetos designados de uma origem de dados ligados e os atributos especificados na lista de inclusão de atributos. O motor de sincronização utiliza o espaço de conector para determinar o que foi alterado na origem de dados ligados e testar as alterações de entrada. O motor de sincronização também utiliza o espaço conector para testar as alterações de saída para exportação para a origem de dados ligada. O motor de sincronização mantém um espaço conector distintas como uma área de teste para cada conector.

Ao utilizar uma área de teste, o motor de sincronização permanece independente das origens de dados ligados e não é afetado pela disponibilidade e acessibilidade. Como resultado, pode processar informações de identidade em qualquer altura utilizando os dados na área de teste. O motor de sincronização pode pedir apenas as alterações feitas no interior da origem de dados ligada desde a última sessão de comunicação terminada ou push apenas as alterações às informações de identidade que a origem de dados ligada ainda não tiver recebido, que reduz a rede tráfego entre o motor de sincronização e a origem de dados ligada.

Além disso, o motor de sincronização armazena informações de estado sobre todos os objetos que ele prepara no espaço conector. Quando são recebidos novos dados, o motor de sincronização avalia sempre se os dados já foram sincronizados.

O **metaverso** é uma área de armazenamento que contém as informações de identidade agregadas de várias origens de dados ligadas, fornecendo uma única vista global e integrada de todos os objetos combinadas. São criados com base nas informações de identidade que são obtidas a partir de origens de dados ligadas e um conjunto de regras que permitem-lhe personalizar o processo de sincronização de objetos de Metaverso.

A ilustração seguinte mostra o espaço de nomes de espaço conector e o espaço de nomes de metaverso dentro do motor de sincronização.

![Arch2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objetos de identidade do motor de sincronização
Os objetos no motor de sincronização são representações de qualquer um dos objetos na origem de dados ligada ou a vista integrada que o motor de sincronização tem desses objetos. Todos os objetos de motor de sincronização tem de ter um identificador exclusivo global (GUID). GUIDs fornecem a integridade dos dados e express relações entre objetos.

### <a name="connector-space-objects"></a>Objetos de espaço conector
Quando o motor de sincronização se comunica com uma origem de dados ligada, lê as informações de identidade na origem de dados ligados e usa essas informações para criar uma representação do objeto de identidade no espaço conector. Não é possível criar ou excluir esses objetos individualmente. No entanto, pode eliminar manualmente todos os objetos no espaço conector.

Todos os objetos no espaço conector tem dois atributos:

* Um identificador exclusivo global (GUID)
* Um nome único (também conhecido como DN)

Se a origem de dados ligada atribui um atributo exclusivo para o objeto, em seguida, objetos no espaço conector também podem ter um atributo de âncora. O atributo de âncora identifica exclusivamente um objeto na origem de dados ligada. O motor de sincronização utiliza a âncora para localizar a representação correspondente deste objeto na origem de dados ligada. Motor de sincronização pressupõe que nunca mudará a âncora de um objeto durante o ciclo de vida do objeto.

Muitos dos conectores utilizam um identificador exclusivo conhecido para gerar uma âncora automaticamente para cada objeto quando for importado. Por exemplo, o conector do Active Directory utiliza a **objectGUID** atributo para uma âncora. Para origens de dados ligada que não fornecem um identificador exclusivo claramente definido, pode especificar a geração de âncora como parte da configuração do conector.

Nesse caso, a âncora é criada a partir de um ou mais atributos exclusivos de um objeto escreve, nem as alterações e, nesse exclusivamente identifica o objeto no espaço conector (por exemplo, um número do funcionário ou um ID de utilizador).

Um objeto de espaço conector pode ser um dos seguintes:

* Um objeto de teste
* Um marcador de posição

### <a name="staging-objects"></a>Objetos de teste
Um objeto de teste representa uma instância dos tipos de objeto designado da origem de dados ligada. Além do GUID e o nome único, um objeto de teste tem sempre um valor que indica o tipo de objeto.

Objetos de teste que foram importados sempre têm um valor para o atributo de âncora. Objetos de teste que foram recentemente aprovisionados pelo motor de sincronização e estiver no processo a ser criada numa origem de dados ligada não tem um valor para o atributo de âncora.

Objetos de teste também incluem valores atuais de atributos de negócios e as informações operacionais necessários ao motor de sincronização para executar o processo de sincronização. Informações operacionais incluem sinalizadores que indicam o tipo de atualizações que foram transitados para o objeto de teste. Se um objeto de teste tiver recebido novas informações de identidade da origem de dados ligados que ainda não foram processada, o objeto é sinalizado de forma **importação pendente**. Se um objeto de teste tiver novas informações de identidade que não tenham sido exportadas para a origem de dados conectado, ele é sinalizado de forma **pendentes exportação**.

Um objeto de teste pode ser um objeto de importação ou um objeto de exportação. O motor de sincronização cria um objeto de importação com informações do objeto recebidas a partir da origem de dados ligada. Quando o motor de sincronização recebe informações sobre a existência de um novo objeto que corresponda a um dos tipos de objeto selecionados no conector, ele cria um objeto de importação no espaço conector como uma representação do objeto na origem de dados ligada.

A ilustração seguinte mostra um objeto de importação que representa um objeto na origem de dados ligada.

![Arch3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

O motor de sincronização cria um objeto de exportação com informações sobre o objeto no metaverso. Exportar objetos são exportados para a origem de dados ligados durante a próxima sessão de comunicação. Da perspectiva do motor de sincronização, exportar objetos ainda não existe na origem de dados ligada. Por conseguinte, o atributo de âncora para um objeto de exportação não está disponível. Depois de receber o objeto do motor de sincronização, a origem de dados ligada cria um valor exclusivo para o atributo de âncora do objeto.

A ilustração seguinte mostra como um objeto de exportação é criado através da utilização de informações de identidade no metaverso.

![Arch4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

O motor de sincronização confirma que a exportação do objeto ao voltar a importar o objeto de origem de dados ligada. Exportar objetos tornam-se importar objetos quando o motor de sincronização recebe-los durante a importação seguinte dessa origem de dados ligada.

### <a name="placeholders"></a>Marcadores de posição
O motor de sincronização utiliza um espaço de nomes simples para armazenar objetos. No entanto, algumas origens de dados ligadas, como o Active Directory utilizam um espaço de nomes hierárquico. Para transformar informações a partir de um espaço de nomes hierárquico num namespace simples, o motor de sincronização utiliza marcadores de posição para preservar a hierarquia.

Cada marcador de posição representa um componente (por exemplo, uma unidade organizacional) de nomes hierárquico de um objeto que não tenha sido importado para o motor de sincronização, mas é necessário para construir o nome hierárquico. Eles preencher as lacunas criadas pelas referências na origem de dados ligada para objetos que não são objetos no espaço conector de teste.

O motor de sincronização também usa os marcadores de posição para armazenar os objetos referenciados que ainda não tiverem sido importados. Por exemplo, se a sincronização está configurada para incluir o atributo de gestor para o *Abbie Spencer* objeto e o valor recebido é um objeto que não foi importado, tal como *CN = Lee Sperry, CN = Users, DC = fabrikam, DC = com* , as informações de manager são armazenadas como marcadores de posição no espaço conector. Se o objecto do Gestor de mais tarde é importado, o objeto de marcador de posição é substituído pelo objeto de teste que representa o gestor.

### <a name="metaverse-objects"></a>Objetos de Metaverso
Um objeto de metaverso contém a vista agregada desse motor de sincronização tem dos objetos de testes no espaço conector. Motor de sincronização cria objetos de metaverso usando as informações em objetos de importação. Vários objetos de espaço conector podem ser associados a um objeto de metaverso única, mas um objeto de espaço conector não pode ser associado a mais do que um objeto de metaverso.

Objetos de Metaverso não podem ser criados manualmente ou eliminados. O motor de sincronização elimina automaticamente os objetos de metaverso que não têm uma ligação a qualquer objeto de espaço conector no espaço conector.

Para mapear objetos dentro de uma origem de dados ligada para um tipo de objeto correspondente no metaverse, o motor de sincronização fornece um esquema extensível com um conjunto predefinido de tipos de objetos e atributos associados. Pode criar novos tipos de objetos e atributos de objetos de metaverso. Atributos podem ser valor único ou com múltiplos valores e os tipos de atributo podem ser cadeias de caracteres, referências, números e valores booleanos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relações entre objetos de testes e objetos de metaverso
Dentro do espaço de nomes do motor de sincronização, o fluxo de dados está ativado pela relação de ligação entre objetos de testes e objetos de metaverso. Um objeto de teste que está ligado a um objeto de metaverso é chamado um **associado ao objeto** (ou **objeto conector**). Um objeto de teste que não está ligado a um objeto de metaverso é chamado um **desagregado de objeto** (ou **objetos de seccionador**). Os termos associado e desagregado preferidos para não confundir com os conectores responsáveis por importar e exportar dados de um diretório conectado.

Marcadores de posição nunca são associados a um objeto de metaverso

Um associado a um objeto é composto por um objeto de teste e sua relação ligada a um objeto de metaverso único. Objetos associados são utilizados para sincronizar os valores de atributo entre um objeto de espaço conector e um objeto de metaverso.

Quando um objeto de teste se tornar um objeto associado ao durante a sincronização, os atributos podem fluir entre o objeto de teste e o objeto de metaverso. Fluxo de atributos é bidirecional e é configurado ao utilizar regras de atributo de importação e exportação atributo regras.

Objeto de espaço num único conector pode ser ligado a apenas um objeto de metaverso. No entanto, cada objeto de metaverso pode ser associado a vários objetos de espaço conector no mesmo ou nos espaços de conector diferentes, conforme mostrado na ilustração seguinte.

![Arch5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Relação entre o objeto de teste e um objeto de metaverso ligada é persistente e pode ser removida apenas por regras que especificar.

Um objeto disjoined é um objeto de teste que não está ligado a qualquer objeto de metaverso. O atributo de valores de um objeto disjoined não são processados qualquer ainda mais no metaverso. Os valores de atributo do objeto correspondente na origem de dados ligados não são atualizados pelo motor de sincronização.

Usando objetos desagregados, pode armazenar informações de identidade no motor de sincronização e processá-lo mais tarde. Manter um objeto de teste como um objeto disjoined no espaço conector tem muitas vantagens. Uma vez que o sistema já tiver testado as informações necessárias sobre este objecto, não é necessário criar uma representação deste objeto novamente durante a importação seguinte da origem de dados ligada. Dessa forma, o motor de sincronização tem sempre um instantâneo completo da origem de dados ligados, mesmo que não existe nenhuma ligação atual para a origem de dados ligada. Desagregado objetos podem ser convertidos em objetos associados a um e vice-versa, consoante as regras que especificar.

Um objeto de importação é criado como um objeto disjoined. Um objeto de exportação tem de ser um objeto associado a um. A lógica de sistema aplica esta regra e elimina todos os objetos de exportação não é um objeto associado a um.

## <a name="sync-engine-identity-management-process"></a>Processo de gestão de identidade de motor de sincronização
O processo de gerenciamento de identidade controla a forma como as informações de identidade são atualizadas entre origens de dados ligadas diferentes. Gestão de identidades ocorre em três processos:

* Importar
* Sincronização
* Exportar

Durante o processo de importação, o motor de sincronização avalia as informações de identidade de entrada de uma origem de dados ligadas. Quando forem detetadas alterações, ele cria novos objetos de testes ou objetos de testes existentes no espaço conector para a sincronização de atualizações.

Durante o processo de sincronização, o motor de sincronização de atualizações de metaverso para refletir as alterações que ocorreram no espaço conector e atualiza o espaço conector para refletir as alterações que ocorreram no metaverso.

Durante o processo de exportação, o motor de sincronização envia por push as alterações que são testados nos objetos de teste e que são sinalizados como pendentes de exportação.

A ilustração a seguir mostra onde cada um dos processos ocorre como fluxos de informações de identidade de uma origem de dados ligada para outro.

![Arch6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Processo de importação
Durante o processo de importação, o motor de sincronização avalia atualizações às informações de identidade. Motor de Sincronização compara as informações de identidade recebidas da origem de dados ligados com as informações de identidade sobre um objeto de teste e determina se o objeto de teste requer atualizações. Se for necessário atualizar o objeto de teste com novos dados, o objeto de teste é sinalizado como os importação pendente.

Com o armazenamento temporário objetos no espaço conector antes de sincronização, o motor de sincronização pode processar apenas as informações de identidade que foi alterado. Este processo fornece as seguintes vantagens:

* **Sincronização eficiente**. A quantidade de dados processados durante a sincronização é minimizada.
* **A ressincronização eficiente**. Pode alterar como o motor de sincronização processa as informações de identidade sem ligar novamente o motor de sincronização para a origem de dados.
* **Oportunidade de pré-visualizar sincronização**. Pode visualizar a sincronização para verificar se suas suposições sobre o processo de gestão de identidade estão corretas.

Para cada objeto especificado no conector, o motor de sincronização primeiro tenta localizar uma representação do objeto no espaço conector do conector. Motor de sincronização examina todos os objetos de testes no espaço conector e tenta encontrar um objeto de transição correspondente que tem um atributo de âncora correspondente. Se nenhum objeto de teste existente tem um atributo de âncora correspondente, o motor de sincronização tenta encontrar um objeto de transição correspondente com o mesmo nome distinto.

Quando o motor de sincronização encontra um objeto de teste que corresponda ao nome único, mas não por âncora, ocorre o seguinte comportamento especial:

* Se o objeto localizado no espaço conector não tem nenhuma âncora, o motor de sincronização remove este objeto de espaço conector e marca o objeto de metaverso está ligado como **Repita aprovisionamento na próxima sincronização executar**. Em seguida, ele cria o novo objeto de importação.
* Se o objeto localizado no espaço conector tem uma âncora, em seguida, motor de sincronização parte do princípio de que este objeto foi mudado ou eliminado no diretório conectado. Atribui um nome único temporário, novo para o objeto de espaço conector para que ele pode preparar o objeto de entrada. O objeto antigo de, em seguida, se torna **transitório**, esperar até que o conector importar a mudança de nome ou eliminação para resolver a situação.

Se o motor de sincronização localiza um objeto de transição correspondente para o objeto especificado no conector, ele determina que tipo de alterações a aplicar. Por exemplo, motor de sincronização poderá mudar o nome ou eliminar o objeto na origem de dados ligada ou ele só poderá atualizar valores de atributo do objeto.

Objetos de teste com dados atualizados são marcados como os importação pendente. Diferentes tipos de pendentes imports estão disponíveis. Consoante o resultado do processo de importação, um objeto de teste no espaço conector tem um dos seguintes pendentes tipos de importação:

* **Nenhuma**. Nenhuma alteração a qualquer um dos atributos do objeto de teste está disponíveis. Motor de sincronização não sinaliza esse tipo como os importação pendente.
* **Adicionar**. O objeto de teste é um novo objeto de importação no espaço conector. Motor de Sincronização sinaliza esse tipo como pendentes importação para processamento adicional no metaverso.
* **Atualização**. Motor de sincronização encontra um objeto de transição correspondente no espaço conector e sinaliza esse tipo como importação pendente para que as atualizações para os atributos podem ser processadas no metaverso. As atualizações incluem a mudar o nome de objeto.
* **Eliminar**. Motor de sincronização encontra um objeto de transição correspondente no espaço conector e sinaliza esse tipo como importação pendente para que o objeto associado ao pode ser eliminado.
* **Adicionar/eliminar**. Motor de sincronização encontra um objeto de transição correspondente no espaço conector, mas os tipos de objeto não coincidem. Neste caso, um adicionar delete modificação é preparada. A eliminar-adicionar modificação indica ao motor de sincronização que tem de ocorrer uma ressincronização completa deste objeto porque diferentes conjuntos de regras se aplica a este objeto quando o objeto de tipo de alterações.

Ao definir o estado de importação de um objeto de teste, é possível reduzir significativamente a quantidade de dados processados durante a sincronização porque a permissão para que o sistema processar apenas os objetos que os dados atualizados.

### <a name="synchronization-process"></a>Processo de sincronização
Sincronização consiste em dois processos relacionados:

* Sincronização de entrada, quando o conteúdo do metaverso é atualizado com os dados no espaço conector.
* Sincronização de saída, quando o conteúdo do espaço conector é atualizado com dados no metaverso.

Ao utilizar as informações de teste no espaço conector, o processo de sincronização de entrada cria, no metaverso, a vista integrada dos dados que são armazenados em origens de dados ligadas. São agregadas todos os objetos de testes ou apenas as pessoas com informações de importação, dependendo de como as regras estão configuradas.

As atualizações do processo de sincronização de saída exportar objetos quando alterar os objetos de metaverso.

Sincronização de entrada cria a vista integrada no metaverso as informações de identidade que é recebido a partir de origens de dados ligadas. Motor de sincronização pode processar informações de identidade em qualquer altura, utilizando as últimas informações de identidade que ele tenha da origem de dados ligada.

**Sincronização de entrada**

Sincronização de entrada inclui os seguintes processos:

* **Aprovisionar** (também denominado **projeção** se é importante distinguir esse processo de aprovisionamento de sincronização de saída). O motor de sincronização cria um novo objeto de metaverso com base num objeto de teste e liga-los. Aprovisionar é uma operação de ao nível do objeto.
* **Junte-se a**. O motor de sincronização liga a um objeto de transição para um objeto de metaverso existente. Uma junção é uma operação de ao nível do objeto.
* **Importar Fluxo de atributos**. Motor de sincronização atualiza os valores de atributo, chamados de fluxo de atributos, do objeto no metaverso. Fluxo de atributos de importação é uma operação de nível de atributo que exige uma ligação entre um objeto de teste e um objeto de metaverso.

Disposição é o único processo que cria os objetos no metaverso. Aprovisionar afeta apenas os objetos de importação que são objetos desagregados. Durante o aprovisionamento, o motor de sincronização cria um objeto de metaverso que corresponde ao tipo de objeto do objeto de importação e estabelece uma ligação entre os dois objetos, criando assim um objeto associado a um.

O processo de ingresso também estabelece uma ligação entre objetos de importação e um objeto de metaverso. A diferença entre a associação e a disposição é que o processo de ingresso requer que o objeto de importação são associados a um objeto de metaverso existente, onde o processo de aprovisionamento cria um novo objeto de metaverso.

Motor de sincronização tenta associar um objeto de importação para um objeto de metaverso utilizando os critérios especificados na configuração da regra de sincronização.

Durante os processos de aprovisionamento e a associação, o motor de sincronização liga um objeto disjoined a um objeto de metaverso, tornando-os associado. Depois destas operações ao nível do objeto são concluídas, o motor de sincronização pode atualizar os valores de atributo do objeto de metaverso associados. Este processo é chamado de fluxo de atributos de importação.

Importar Fluxo de atributos ocorre em todos os objetos de importação que transportar dados novo e são associados a um objeto de metaverso.

**Sincronização de saída**

Atualizações de sincronização de saída exportar objetos quando um objeto de metaverso alterar, mas não é eliminado. O objetivo da sincronização de saída é avaliar se as alterações a objetos de metaverso necessitam de atualizações para objetos de testes nos espaços de conector. Em alguns casos, as alterações podem exigir esse teste objetos em todos os espaços de conector ser atualizado. Objetos de teste que são alterados são sinalizados como pendentes de exportação, tornando-os exportar objetos. Estes objetos são mais tarde instalados sem solicitação à origem de dados ligados durante o processo de exportação de exportação.

Sincronização de saída tem três processos:

* **Aprovisionamento**
* **O desaprovisionamento**
* **Fluxo de atributos da exportação**

Aprovisionamento e desaprovisionamento são as duas operações ao nível do objeto. Desaprovisionamento depende do aprovisionamento porque aprovisionamento apenas pode iniciar ele. Desaprovisionamento é acionado quando o aprovisionamento remove a ligação entre um objeto de metaverso e um objeto de exportação.

Aprovisionamento sempre é acionado quando as alterações são aplicadas a objetos no metaverso. Quando forem feitas alterações a objetos de metaverso, o motor de sincronização pode efetuar qualquer uma das seguintes tarefas como parte do processo de aprovisionamento:

* Crie objetos associados, onde um objeto de metaverso está anexado a um objeto de exportação criado recentemente.
* Mudar o nome de um objeto associado a um.
* Anular adesão vínculos entre um objeto de metaverso e objetos, criação de um objeto disjoined de teste.

Se o aprovisionamento requer o motor de sincronização para criar um novo objeto de conector, o objeto de transição para o qual o objeto de metaverso está ligado é sempre um objeto de exportação, porque o objeto ainda não existir na origem de dados ligada.

Se o aprovisionamento requer o motor de sincronização para anular adesão um objeto associado ao, criar um objeto disjoined, desaprovisionamento é acionado. O processo de desaprovisionamento elimina o objeto.

Durante o desaprovisionamento, a eliminação de um objeto de exportação não elimina fisicamente o objeto. O objeto é sinalizado de forma **eliminado**, que significa que a operação de eliminação é preparada no objeto.

Fluxo de atributos da exportação também ocorre durante o processo de sincronização de saída, semelhante à forma como o que o fluxo de atributos de importação ocorre durante a sincronização de entrada. Fluxo de atributos da exportação ocorre apenas entre objetos de metaverso e exportação que estão associados.

### <a name="export-process"></a>Processo de exportação
Durante o processo de exportação, o motor de sincronização examina todos os objetos de exportação que são sinalizados como pendentes exportação no espaço conector e, em seguida, envia atualizações para a origem de dados ligada.

O motor de sincronização pode determinar o sucesso de uma exportação, mas ele não é suficientemente determinar que o processo de gerenciamento de identidade foi concluído. Objetos na origem de dados ligada sempre podem ser alterados por outros processos. Porque o motor de sincronização não tem uma ligação persistente para a origem de dados ligadas, não é suficiente fazer suposições sobre as propriedades de um objeto na origem de dados ligada com base apenas numa notificação de exportação com êxito.

Por exemplo, um processo na origem de dados ligadas foi possível alterar os atributos do objeto para os valores originais (ou seja, a origem de dados ligada poderia substituir os valores imediatamente depois dos dados são instalados sem solicitação pelo motor de sincronização e aplicados com êxito em a origem de dados ligadas).

Os arquivos de motor de sincronização exportar e importar informações de estado sobre cada objeto de teste. Se os valores dos atributos que são especificados na lista de inclusão de atributos foram alterados desde a última exportação, o armazenamento de importar e exportar o motor de sincronização do Estado ativa para reagir de forma adequada. Motor de sincronização utiliza o processo de importação para confirmar os valores de atributos que foram exportados para a origem de dados ligada. Uma comparação entre as informações de importados e exportadas, conforme mostrado na ilustração seguinte, permite que o motor de sincronização determinar se a exportação foi concluída com êxito ou se ele precisará ser repetido.

![Arch7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Por exemplo, se o motor de sincronização exporta o atributo C, que tem um valor de 5, a uma origem de dados conectado, ele armazena C = 5 na sua memória de estado de exportação. Cada exportação adicional para este objeto resulta numa tentativa para exportar C = 5 para a origem de dados ligada novamente porque o motor de sincronização parte do princípio de que este valor não foi persistentemente aplicado ao objeto (ou seja, a menos que um valor diferente foi importado recentemente a partir do origem de dados ligada). A memória de exportação é limpos quando C = 5 é recebido durante uma operação de importação no objeto.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

