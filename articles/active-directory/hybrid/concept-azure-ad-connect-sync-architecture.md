---
title: 'Sincronização Azure AD Connect: Compreender a arquitetura - Azure'
description: Este tópico descreve a arquitetura da sincronização Azure AD Connect e explica os termos utilizados.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261622"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronização Azure AD Connect: Compreender a arquitetura
Este tópico abrange a arquitetura básica para a sincronização Azure AD Connect. Em muitos aspetos, é semelhante aos seus antecessores MIIS 2003, ILM 2007 e FIM 2010. O sincronizado Azure AD Connect é a evolução destas tecnologias. Se estiver familiarizado com alguma destas tecnologias anteriores, o conteúdo deste tópico também lhe será familiar. Se é novo em sincronização, então este tópico é para si. No entanto, não é um requisito conhecer os detalhes deste tópico para ser bem sucedido na realização de personalizações para a sincronização Azure AD Connect (chamada motor de sincronização neste tópico).

## <a name="architecture"></a>Arquitetura
O motor sincronizado cria uma visão integrada de objetos que são armazenados em múltiplas fontes de dados conectadas e gere informações de identidade nessas fontes de dados. Esta visão integrada é determinada pela informação de identidade obtida a partir de fontes de dados conectadas e um conjunto de regras que determinam como processar esta informação.

### <a name="connected-data-sources-and-connectors"></a>Fontes e Conectores de Dados Conectados
O motor sincronizado processa informações de identidade de diferentes repositórios de dados, tais como Ative Directory ou uma base de dados do SQL Server. Todos os repositórios de dados que organizam os seus dados num formato semelhante a uma base de dados e que fornece métodos padrão de acesso a dados são um potencial candidato à fonte de dados para o motor de sincronização. Os repositórios de dados sincronizados por motor sincronizado são chamados fontes de **dados conectadas** ou **diretórios conectados** (CD).

O motor sincronizado engloba a interação com uma fonte de dados conectada dentro de um módulo chamado **Conector**. Cada tipo de fonte de dados conectada tem um Conector específico. O Conector traduz uma operação necessária para o formato que a fonte de dados conectada compreende.

Os conectores fazem chamadas aPi para trocar informações de identidade (leia e escreva) com uma fonte de dados conectada. Também é possível adicionar um Conector personalizado utilizando a estrutura de conectividade extensível. A ilustração que se segue mostra como um Conector liga uma fonte de dados ligada ao motor de sincronização.

![Arco1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Os dados podem fluir em ambas as direções, mas não podem fluir em ambas as direções simultaneamente. Por outras palavras, um Conector pode ser configurado para permitir que os dados fluam da fonte de dados conectada para sincronizar o motor ou do motor sincronizado para a fonte de dados conectada, mas apenas uma dessas operações pode ocorrer a qualquer momento para um objeto e atributo. A direção pode ser diferente para diferentes objetos e para diferentes atributos.

Para configurar um Conector, especifice os tipos de objetos que pretende sincronizar. Especificar os tipos de objetos define o âmbito dos objetos incluídos no processo de sincronização. O próximo passo é selecionar os atributos para sincronizar, que é conhecido como uma lista de inclusão de atributos. Estas definições podem ser alteradas a qualquer momento em resposta a alterações às suas regras de negócio. Quando utilizar o assistente de instalação Azure AD Connect, estas definições estão configuradas para si.

Para exportar objetos para uma fonte de dados conectada, a lista de inclusão do atributo deve incluir pelo menos os atributos mínimos necessários para criar um tipo de objeto específico numa fonte de dados conectada. Por exemplo, o atributo **sAMAccountName** deve ser incluído na lista de inclusão do atributo para exportar um objeto de utilizador para Ative Directory, porque todos os objetos do utilizador no Diretório Ativo devem ter um atributo **sAMAccountName** definido. Mais uma vez, o assistente de instalação faz esta configuração para si.

Se a fonte de dados conectada utilizar componentes estruturais, tais como divisórias ou contentores para organizar objetos, pode limitar as áreas da fonte de dados conectadas que são utilizadas para uma determinada solução.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estrutura interna do espaço de nome do motor sincronizado
Todo o espaço de nomedo do motor sincronizado é composto por dois espaços de nome que armazenam a informação de identidade. Os dois espaços de nome são:

* O espaço do conector (CS)
* O metaverso (MV)

O **espaço do conector** é uma área de preparação que contém representações dos objetos designados a partir de uma fonte de dados conectada e os atributos especificados na lista de inclusão do atributo. O motor sincronizado utiliza o espaço do conector para determinar o que mudou na fonte de dados conectada e para encenar alterações de entrada. O motor sincronizado também utiliza o espaço do conector para encenar alterações de saída para exportação para a fonte de dados conectada. O motor sincronizado mantém um espaço conector distinto como uma área de preparação para cada Conector.

Utilizando uma área de paragem, o motor sincronizado permanece independente das fontes de dados conectadas e não é afetado pela sua disponibilidade e acessibilidade. Como resultado, pode processar informações de identidade a qualquer momento utilizando os dados na área de encenação. O motor sincronizado só pode solicitar as alterações efetuadas dentro da fonte de dados conectada desde que a última sessão de comunicação terminou ou empurrou apenas as alterações à informação de identidade que a fonte de dados conectada ainda não recebeu, o que reduz a rede tráfego entre o motor de sincronização e a fonte de dados conectada.

Além disso, sincronizar o motor armazena informações de estado sobre todos os objetos que encena no espaço do conector. Quando novos dados são recebidos, o motor sincronizado avalia sempre se os dados já foram sincronizados.

O **metaverso** é uma área de armazenamento que contém a informação de identidade agregada de múltiplas fontes de dados conectadas, proporcionando uma visão única global e integrada de todos os objetos combinados. Os objetos metaversos são criados com base na informação de identidade que é obtida a partir das fontes de dados conectadas e um conjunto de regras que lhe permitem personalizar o processo de sincronização.

A ilustração que se segue mostra o espaço de nome do conector e o espaço de nome metaverso dentro do motor de sincronização.

![Arco2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Sync objetos de identidade do motor
Os objetos no motor de sincronização são representações de ambos os objetos na fonte de dados conectada ou da visão integrada que o motor sincronizado tem desses objetos. Cada objeto do motor sincronizado deve ter um identificador globalmente único (GUID). Os GUIDs fornecem integridade de dados e relações expressas entre objetos.

### <a name="connector-space-objects"></a>Objetos espaciais conector
Quando o motor sincronizado comunica com uma fonte de dados conectada, lê a informação de identidade na fonte de dados conectada e utiliza essa informação para criar uma representação do objeto de identidade no espaço do conector. Não é possível criar ou eliminar estes objetos individualmente. No entanto, pode eliminar manualmente todos os objetos num espaço de conector.

Todos os objetos no espaço do conector têm dois atributos:

* Um identificador globalmente único (GUID)
* Um nome distinto (também conhecido como DN)

Se a fonte de dados conectada atribuir um atributo único ao objeto, então os objetos no espaço do conector também podem ter um atributo de âncora. O atributo de âncora identifica um objeto exclusivamente na fonte de dados conectada. O motor sincronizado utiliza a âncora para localizar a representação correspondente deste objeto na fonte de dados ligada. O motor sync assume que a âncora de um objeto nunca muda ao longo da vida do objeto.

Muitos dos Conectores usam um identificador único conhecido para gerar uma âncora automaticamente para cada objeto quando é importado. Por exemplo, o Conector de Diretório Ativo utiliza o atributo **objetivo PARA** uma âncora. Para fontes de dados conectadas que não fornecem um identificador único claramente definido, pode especificar a geração de âncora como parte da configuração do Conector.

Nesse caso, a âncora é construída a partir de um ou mais atributos únicos de um tipo de objeto, nenhum dos quais muda, e que identifica exclusivamente o objeto no espaço do conector (por exemplo, um número de empregado ou um ID do utilizador).

Um objeto espacial conector pode ser um dos seguintes:

* Um objeto de encenação
* Um espaço reservado

### <a name="staging-objects"></a>Objetos de encenação
Um objeto de encenação representa uma instância dos tipos de objetos designados a partir da fonte de dados conectada. Além do GUID e do nome distinto, um objeto de encenação tem sempre um valor que indica o tipo de objeto.

Os objetos de encenação que foram importados têm sempre um valor para o atributo de âncora. A encenação de objetos que tenham sido recentemente aprovisionados pelo motor sync e que estejam em vias de ser criados na fonte de dados conectada não têm um valor para o atributo de âncora.

Os objetos de encenação também transportam valores atuais de atributos de negócio, e informações operacionais necessárias pelo motor de sincronização para realizar o processo de sincronização. As informações operacionais incluem bandeiras que indicam o tipo de atualizações que são encenadas no objeto de encenação. Se um objeto de encenação tiver recebido novas informações de identidade da fonte de dados conectadas que ainda não foram processadas, o objeto é sinalizado como **pendente de importação.** Se um objeto de encenação tiver novas informações de identidade que ainda não foram exportadas para a fonte de dados ligadas, é sinalizada como **exportação pendente.**

Um objeto de preparação pode ser um objeto de importação ou um objeto de exportação. O motor sincronizado cria um objeto de importação utilizando informações de objetos recebidas a partir da fonte de dados conectada. Quando o motor sincronizado recebe informações sobre a existência de um novo objeto que corresponde a um dos tipos de objetos selecionados no Conector, cria um objeto de importação no espaço do conector como representação do objeto na fonte de dados conectada.

A ilustração seguinte mostra um objeto de importação que representa um objeto na fonte de dados conectada.

![Arco3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

O motor sincronizado cria um objeto de exportação utilizando informações sobre objetos no metaverso. Os objetos de exportação são exportados para a fonte de dados ligada durante a próxima sessão de comunicação. Do ponto de vista do motor de sincronização, os objetos de exportação ainda não existem na fonte de dados conectada. Por conseguinte, o atributo de âncora para um objeto de exportação não está disponível. Depois de receber o objeto do motor sincronizado, a fonte de dados conectada cria um valor único para o atributo de âncora do objeto.

A ilustração que se segue mostra como um objeto de exportação é criado utilizando informações de identidade no metaverso.

![Arco4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

O motor sincronizado confirma a exportação do objeto reimportando o objeto da fonte de dados ligada. Os objetos de exportação tornam-se objetos de importação quando o motor sincronizado os recebe durante a próxima importação a partir dessa fonte de dados ligada.

### <a name="placeholders"></a>Marcadores de Posição
O motor sincronizado usa um espaço de nome plano para armazenar objetos. No entanto, algumas fontes de dados conectadas, como o Ative Directory, utilizam um espaço de nome hierárquico. Para transformar a informação de um espaço de nome hierárquico num espaço de nome plano, o motor sincronizado utiliza espaços reservados para preservar a hierarquia.

Cada espaço reservado representa um componente (por exemplo, uma unidade organizacional) do nome hierárquico de um objeto que não foi importado para o motor de sincronização, mas que é obrigado a construir o nome hierárquico. Preenchem lacunas criadas por referências na fonte de dados conectadas a objetos que não estão a encenar objetos no espaço do conector.

O motor de sincronização também utiliza espaços reservados para armazenar objetos referenciados que ainda não foram importados. Por exemplo, se a sincronização for configurada para incluir o atributo do gestor para o objeto *Abbie Spencer* e o valor recebido for um objeto que ainda não foi importado, como *CN=Lee Sperry,CN=Users,DC=fabrikam,DC=com,* a informação do gestor é armazenada como espaço reservado no espaço do conector. Se o objeto de gerente for posteriormente importado, o objeto de espaço reservado é substituído pelo objeto de encenação que representa o gestor.

### <a name="metaverse-objects"></a>Objetos metaversais
Um objeto metaverso contém a visão agregada que o motor sincronizado tem dos objetos de paragem no espaço do conector. O motor sync cria objetos metaversais utilizando a informação em objetos de importação. Vários objetos espaciais do conector podem ser ligados a um único objeto metaverso, mas um objeto espacial conector não pode ser ligado a mais do que um objeto metaverso.

Os objetos metaversos não podem ser criados ou eliminados manualmente. O motor sincronizado elimina automaticamente objetos metaversais que não têm uma ligação a qualquer objeto espacial conector no espaço do conector.

Para mapear objetos dentro de uma fonte de dados conectada a um tipo de objeto correspondente dentro do metaverso, o motor sincronizado fornece um esquema extensível com um conjunto predefinido de tipos de objetos e atributos associados. Pode criar novos tipos de objetos e atributos para objetos metaversais. Os atributos podem ser de valor único ou multivalorizados, e os tipos de atributos podem ser cordas, referências, números e valores booleanos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relações entre objetos de encenação e objetos metaversos
Dentro do espaço de nome do motor sincronizado, o fluxo de dados é ativado pela relação de ligação entre objetos de paragem e objetos metaversos. Um objeto de encenação que está ligado a um objeto metaverso é chamado **de objeto unificado** (ou **objeto de conector).** Um objeto de encenação que não esteja ligado a um objeto metaverso é chamado de **objeto desunido** (ou **objeto de desconexor).** Os termos aderidos e desacompanhados são preferidos para não confundir com os Conectores responsáveis pela importação e exportação de dados de um diretório ligado.

Os espaços reservados nunca estão ligados a um objeto metaverso

Um objeto unido compreende um objeto de encenação e a sua relação ligada a um único objeto metaverso. Os objetos unidos são usados para sincronizar valores de atributoentre um objeto espacial conector e um objeto metaverso.

Quando um objeto de encenação se torna um objeto unido durante a sincronização, os atributos podem fluir entre o objeto de encenação e o objeto metaverso. O fluxo de atributos é bidirecional e é configurado utilizando regras de atribuição de importação e regras de atribuição de exportação.

Um único objeto espacial conector pode ser ligado apenas a um objeto metaverso. No entanto, cada objeto metaverso pode ser ligado a múltiplos objetos espaciais conector nos mesmos ou em diferentes espaços de conector, como mostra a seguinte ilustração.

![Arco 5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

A relação ligada entre o objeto de encenação e um objeto metaverso é persistente e só pode ser removida por regras que especifica.

Um objeto desadesíado é um objeto de encenação que não está ligado a qualquer objeto metaverso. Os valores de atributo de um objeto desunido não são processados mais dentro do metaverso. Os valores de atributo do objeto correspondente na fonte de dados conectada não são atualizados pelo motor sincronizado.

Ao utilizar objetos desvinculados, pode armazenar informações de identidade no motor de sincronização e processá-la mais tarde. Manter um objeto de encenação como um objeto desunido no espaço do conector tem muitas vantagens. Uma vez que o sistema já encenou as informações necessárias sobre este objeto, não é necessário voltar a criar uma representação deste objeto durante a próxima importação a partir da fonte de dados conectada. Desta forma, o motor sincronizado tem sempre uma imagem completa da fonte de dados conectada, mesmo que não exista uma ligação atual à fonte de dados conectada. Os objetos desintegrados podem ser convertidos em objetos unidos, e vice-versa, dependendo das regras que especifica.

Um objeto de importação é criado como um objeto desafiado. Um objeto de exportação deve ser um objeto unido. A lógica do sistema aplica esta regra e elimina todos os objetos de exportação que não são um objeto unido.

## <a name="sync-engine-identity-management-process"></a>Processo de gestão da identidade do motor sincronizado
O processo de gestão de identidade controla a forma como a informação de identidade é atualizada entre diferentes fontes de dados conectadas. A gestão da identidade ocorre em três processos:

* Importar
* Sincronização
* Exportar

Durante o processo de importação, o motor sincronizado avalia as informações de identidade provenientes de uma fonte de dados conectada. Quando são detetadas alterações, cria novos objetos de encenação ou atualiza os objetos de preparação existentes no espaço do conector para sincronização.

Durante o processo de sincronização, o motor sincronizado atualiza o metaverso para refletir as alterações que ocorreram no espaço do conector e atualiza o espaço do conector para refletir as alterações que ocorreram no metaverso.

Durante o processo de exportação, o motor sincronizado empurra para fora as alterações que são encenadas em objetos de preparação e que são sinalizadas como exportação pendente.

A seguinte ilustração mostra onde cada um dos processos ocorre à medida que a informação de identidade flui de uma fonte de dados conectada para outra.

![Arco6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Processo de importação
Durante o processo de importação, o motor sincronizado avalia as atualizações à informação de identidade. O motor Sync compara as informações de identidade recebidas da fonte de dados conectadas com as informações de identidade sobre um objeto de encenação e determina se o objeto de paragem requer atualizações. Se for necessário atualizar o objeto de preparação com novos dados, o objeto de preparação é sinalizado como importação pendente.

Ao encenar objetos no espaço do conector antes da sincronização, o motor de sincronização só pode processar a informação de identidade que mudou. Este processo proporciona os seguintes benefícios:

* **Sincronização eficiente.** A quantidade de dados processados durante a sincronização é minimizada.
* **Resincronização eficiente.** Pode alterar a forma como o motor sincronizado processa informações de identidade sem voltar a ligar o motor de sincronização à fonte de dados.
* **Oportunidade de pré-visualizar sincronização.** Pode pré-visualizar a sincronização para verificar se as suas suposições sobre o processo de gestão de identidade estão corretas.

Para cada objeto especificado no Conector, o motor sincronizado tenta primeiro localizar uma representação do objeto no espaço do conector do Conector. O motor Sync examina todos os objetos de preparação no espaço do conector e tenta encontrar um objeto de encenação correspondente que tenha um atributo de âncora correspondente. Se nenhum objeto de preparação existente tiver um atributo de âncora correspondente, o motor sincronizado tenta encontrar um objeto de encenação correspondente com o mesmo nome distinto.

Quando o motor sincronizado encontra um objeto de encenação que corresponde ao nome distinto, mas não por âncora, ocorre o seguinte comportamento especial:

* Se o objeto localizado no espaço do conector não tiver âncora, então o motor de sincronização remove este objeto do espaço do conector e marca o objeto metaverso a que está ligado como **retry provisionamento na próxima execução**de sincronização . Depois cria o novo objeto de importação.
* Se o objeto localizado no espaço do conector tiver uma âncora, então o motor sincronizado assume que este objeto foi renomeado ou eliminado no diretório ligado. Atribui um nome temporário e novo e distinto para o objeto espacial do conector para que possa encenar o objeto de entrada. O objeto antigo torna-se então **transitório,** à espera que o Conector importe o renome ou a supressão para resolver a situação.

Se o motor sincronizado localizar um objeto de paragem que corresponde ao objeto especificado no Conector, determina que tipo de alterações aplicar. Por exemplo, o motor de sincronização pode mudar o nome ou apagar o objeto na fonte de dados conectada, ou pode apenas atualizar os valores de atributo do objeto.

A encenação de objetos com dados atualizados é marcada como importação pendente. Estão disponíveis diferentes tipos de importações pendentes. Dependendo do resultado do processo de importação, um objeto de preparação no espaço do conector tem um dos seguintes tipos de importação pendentes:

* **Nenhuma.** Não existem alterações em nenhum dos atributos do objeto de encenação. O motor sync não assinala este tipo como importação pendente.
* **Adicione**. O objeto de preparação é um novo objeto de importação no espaço do conector. O motor sync sinaliza este tipo como uma importação pendente para processamento adicional no metaverso.
* **Atualização**. O motor Sync encontra um objeto de paragem correspondente no espaço do conector e sinaliza este tipo como importação pendente para que as atualizações aos atributos possam ser processadas no metaverso. As atualizações incluem o renomeação de objetos.
* **Apagar**. O motor Sync encontra um objeto de preparação correspondente no espaço do conector e sinaliza este tipo como importação pendente para que o objeto unido possa ser eliminado.
* **Eliminar/Adicionar**. O motor Sync encontra um objeto de preparação correspondente no espaço do conector, mas os tipos de objetos não correspondem. Neste caso, é encenada uma modificação de adição de eliminação. Uma modificação de adição de eliminação indica ao motor de sincronização que deve ocorrer uma resincronização completa deste objeto porque diferentes conjuntos de regras se aplicam a este objeto quando o tipo de objeto muda.

Ao definir o estado de importação pendente de um objeto de encenação, é possível reduzir significativamente a quantidade de dados processados durante a sincronização, pois isso permite que o sistema processe apenas os objetos que tenham dados atualizados.

### <a name="synchronization-process"></a>Processo de sincronização
A sincronização consiste em dois processos relacionados:

* Sincronização de entrada, quando o conteúdo do metaverso é atualizado utilizando os dados no espaço do conector.
* Sincronização de saída, quando o conteúdo do espaço do conector é atualizado utilizando dados no metaverso.

Utilizando a informação encenada no espaço do conector, o processo de sincronização de entrada cria no metaverso a visão integrada dos dados armazenados nas fontes de dados conectadas. Todos os objetos de encenação ou apenas aqueles com informações de importação pendentes são agregados, dependendo da configuração das regras.

O processo de sincronização de saída atualiza os objetos de exportação quando os objetos metaversos mudam.

A sincronização de entrada cria a visão integrada no metaverso da informação de identidade que é recebida a partir das fontes de dados conectadas. O motor Sync pode processar informações de identidade a qualquer momento utilizando as informações de identidade mais recentes que possui a partir da fonte de dados conectada.

**Sincronização de entrada**

A sincronização de entrada inclui os seguintes processos:

* **Disposição** (também denominada **Projeção** se for importante distinguir este processo do fornecimento de sincronização de saída). O motor Sync cria um novo objeto metaverso baseado num objeto de encenação e liga-os. A provisão é uma operação ao nível de objetos.
* **Junte-se**a . O motor Sync liga um objeto de preparação a um objeto metaverso existente. Uma adesão é uma operação ao nível de objetos.
* Fluxo de **atributo de importação**. O motor sync atualiza os valores do atributo, chamado fluxo de atributo, do objeto no metaverso. O fluxo de atributos de importação é uma operação de nível de atributo que requer uma ligação entre um objeto de encenação e um objeto metaverso.

A provisão é o único processo que cria objetos no metaverso. A provisão afeta apenas objetos de importação que sejam objetos desadestados. Durante a provisão, o motor sincronizado cria um objeto metaverso que corresponde ao tipo de objeto do objeto de importação e estabelece uma ligação entre ambos os objetos, criando assim um objeto unido.

O processo de adesão também estabelece uma ligação entre objetos de importação e um objeto metaverso. A diferença entre a adesão e a disposição é que o processo de adesão exige que o objeto de importação esteja ligado a um objeto metaverso existente, onde o processo de provisão cria um novo objeto metaverso.

O motor sync tenta juntar um objeto de importação a um objeto metaverso utilizando critérios especificados na configuração da Regra de Sincronização.

Durante a provisão e adesão aos processos, o motor sincronizar liga um objeto desunido a um objeto metaverso, fazendo-os aderir. Após a conclusão destas operações ao nível do objeto, o motor sync pode atualizar os valores de atributo do objeto metaverso associado. Este processo chama-se fluxo de atributo de importação.

O fluxo de atributos de importação ocorre em todos os objetos de importação que transportam novos dados e estão ligados a um objeto metaverso.

**Sincronização de saída**

A sincronização de saída atualiza os objetos de exportação quando um objeto metaverso muda, mas não é eliminado. O objetivo da sincronização de saída é avaliar se as alterações aos objetos metaversos requerem atualizações para encenar objetos nos espaços do conector. Em alguns casos, as alterações podem exigir que a montagem de objetos em todos os espaços do conector seja atualizada. Os objetos de preparação que são alterados são sinalizados como exportação pendente, tornando-os objetos de exportação. Estes objetos de exportação são posteriormente empurrados para a fonte de dados ligadadurante o processo de exportação.

A sincronização de saída tem três processos:

* **Aprovisionamento**
* **Deprovisionamento**
* **Fluxo de atributo de exportação**

O provisionamento e o deprovisionamento são ambas operações ao nível de objetos. A disposição depende do provisionamento, porque só o provisionamento pode iniciá-lo. A disposição é desencadeada quando o fornecimento remove a ligação entre um objeto metaverso e um objeto de exportação.

O fornecimento é sempre acionado quando são aplicadas alterações a objetos no metaverso. Quando são feitas alterações em objetos metaversos, o motor sincronizado pode executar qualquer uma das seguintes tarefas como parte do processo de provisionamento:

* Crie objetos unidos, onde um objeto metaverso está ligado a um objeto de exportação recém-criado.
* Mude o nome de um objeto unido.
* Desafie as ligações entre um objeto metaverso e os objetos de encenação, criando um objeto desunido.

Se o fornecimento requer um motor de sincronização para criar um novo objeto de conector, o objeto de paragem ao qual o objeto metaverso está ligado é sempre um objeto de exportação, porque o objeto ainda não existe na fonte de dados ligada.

Se o fornecimento exigir que o motor de sincronização desintegre um objeto unido, criando um objeto desafogado, é desencadeada a desprovisionamento. O processo de desprovisionamento elimina o objeto.

Durante a desprovisionamento, a eliminação de um objeto de exportação não elimina fisicamente o objeto. O objeto é sinalizado como **eliminado,** o que significa que a operação de eliminação é encenada no objeto.

O fluxo de atributos de exportação também ocorre durante o processo de sincronização de saída, semelhante à forma como o fluxo de atributos de importação ocorre durante a sincronização de entrada. O fluxo de atributos de exportação ocorre apenas entre metaversos e objetos de exportação que são unidos.

### <a name="export-process"></a>Processo de exportação
Durante o processo de exportação, o motor sync examina todos os objetos de exportação que são sinalizados como exportação pendente no espaço do conector, e envia atualizações para a fonte de dados conectada.

O motor sincronizado pode determinar o sucesso de uma exportação, mas não pode determinar suficientemente se o processo de gestão da identidade está concluído. Os objetos na fonte de dados conectados podem sempre ser alterados por outros processos. Uma vez que o motor sincronizado não tem uma ligação persistente à fonte de dados conectada, não é suficiente fazer suposições sobre as propriedades de um objeto na fonte de dados conectada baseada apenas numa notificação de exportação bem sucedida.

Por exemplo, um processo na fonte de dados conectado poderia alterar os atributos do objeto de volta aos seus valores originais (isto é, a fonte de dados conectada poderia sobrepor os valores imediatamente após os dados forem empurrados para fora pelo motor sincronizado e aplicados com sucesso em a fonte de dados conectada).

O motor sincronizado armazena informações sobre o estado de exportação e importação de informações sobre cada objeto de preparação. Se os valores dos atributos especificados na lista de inclusão do atributo tiverem mudado desde a última exportação, o armazenamento do estado de importação e exportação permite que o motor sincronizado reaja adequadamente. O motor Sync utiliza o processo de importação para confirmar valores de atributos que foram exportados para a fonte de dados conectada. Uma comparação entre as informações importadas e exportadas, tal como mostra a seguinte ilustração, permite ao motor sincronizado determinar se a exportação foi bem sucedida ou se tem de ser repetida.

![Arco7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Por exemplo, se as exportações de motores sincronizados atributoC, que tem um valor de 5, a uma fonte de dados conectada, armazena C=5 na sua memória de estado de exportação. Cada exportação adicional deste objeto resulta numa tentativa de exportar C=5 para a fonte de dados conectada novamente porque o motor sincronizado assume que este valor não foi persistentemente aplicado ao objeto (isto é, a menos que um valor diferente tenha sido importado recentemente do fonte de dados conectada). A memória de exportação é limpa quando C=5 é recebida durante uma operação de importação do objeto.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração de [sincronização azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

