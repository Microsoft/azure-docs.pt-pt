---
title: 'Azure AD Connect sync: Understanding the architecture - Azure'
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
ms.openlocfilehash: b27055ce84bbb073045b69b942fd13f4fde4e3b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563867"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect sync: Understanding the architecture
Este tópico abrange a arquitetura básica para a sincronização Azure AD Connect. Em muitos aspetos, é semelhante aos seus antecessores MIIS 2003, ILM 2007 e FIM 2010. Azure AD Connect sync é a evolução destas tecnologias. Se você está familiarizado com alguma destas tecnologias anteriores, o conteúdo deste tópico também lhe será familiar. Se és novo na sincronização, então este tópico é para ti. No entanto, não é um requisito conhecer os detalhes deste tópico para ser bem sucedido em fazer personalizações para a sincronização Azure AD Connect (chamado motor de sincronização neste tópico).

## <a name="architecture"></a>Arquitetura
O motor de sincronização cria uma visão integrada de objetos que são armazenados em múltiplas fontes de dados conectadas e gere informações de identidade nessas fontes de dados. Esta visão integrada é determinada pela informação de identidade obtida a partir de fontes de dados conectadas e por um conjunto de regras que determinam como processar esta informação.

### <a name="connected-data-sources-and-connectors"></a>Fontes de dados conectadas e conectores
O motor de sincronização processa informações de identidade de diferentes repositórios de dados, tais como Ative Directory ou uma base de dados sql Server. Cada repositório de dados que organiza os seus dados num formato semelhante a uma base de dados e que fornece métodos padrão de acesso a dados é um potencial candidato à fonte de dados para o motor de sincronização. Os repositórios de dados sincronizados pelo motor de sincronização são **chamados fontes de dados conectadas** ou **diretórios conectados** (CD).

O motor de sincronização encapsula a interação com uma fonte de dados conectada dentro de um módulo chamado **Conector**. Cada tipo de fonte de dados conectada tem um Conector específico. O Conector traduz uma operação necessária no formato que a fonte de dados conectada compreende.

Os conectores fazem chamadas de API para trocar informações de identidade (tanto ler como escrever) com uma fonte de dados conectada. Também é possível adicionar um Conector personalizado utilizando a estrutura de conectividade extensível. A seguinte ilustração mostra como um Conector liga uma fonte de dados ligada ao motor de sincronização.

![O diagrama mostra uma fonte de dados conectada e um motor de sincronização associado por uma linha chamada Connector.](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Os dados podem fluir em qualquer direção, mas não podem fluir em ambas as direções simultaneamente. Por outras palavras, um Conector pode ser configurado para permitir que os dados fluam da fonte de dados ligada para sincronizar o motor ou do motor de sincronização para a fonte de dados conectada, mas apenas uma dessas operações pode ocorrer a qualquer momento para um objeto e atributo. A direção pode ser diferente para diferentes objetos e para diferentes atributos.

Para configurar um Conector, especifica os tipos de objetos que pretende sincronizar. Especificar os tipos de objetos define o âmbito de objetos incluídos no processo de sincronização. O próximo passo é selecionar os atributos para sincronizar, que é conhecido como uma lista de inclusão de atributos. Estas definições podem ser alteradas a qualquer momento em resposta a alterações às regras do seu negócio. Quando utilizar o assistente de instalação Azure AD Connect, estas definições são configuradas para si.

Para exportar objetos para uma fonte de dados conectada, a lista de inclusão de atributos deve incluir pelo menos os atributos mínimos necessários para criar um tipo de objeto específico numa fonte de dados conectada. Por exemplo, o atributo **sAMAccountName** deve ser incluído na lista de inclusão de atributos para exportar um objeto de utilizador para Ative Directory, porque todos os objetos de utilizador no Ative Directory devem ter um atributo **sAMAccountName** definido. Mais uma vez, o assistente de instalação faz esta configuração para si.

Se a fonte de dados ligada utilizar componentes estruturais, tais como divisórias ou contentores para organizar objetos, pode limitar as áreas na fonte de dados ligada que são utilizadas para uma determinada solução.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estrutura interna do espaço de nome do motor de sincronização
Todo o espaço de nome do motor de sincronização é composto por dois espaços de nome que armazenam a informação de identidade. Os dois espaços de nome são:

* O espaço do conector (CS)
* O metaverso (MV)

O **espaço do conector** é uma área de preparação que contém representações dos objetos designados a partir de uma fonte de dados conectada e dos atributos especificados na lista de inclusão de atributos. O motor de sincronização utiliza o espaço do conector para determinar o que mudou na fonte de dados ligada e para encenar alterações de entrada. O motor de sincronização também utiliza o espaço do conector para encenar alterações de saída para exportação para a fonte de dados conectada. O motor de sincronização mantém um espaço de conector distinto como uma área de preparação para cada Conector.

Ao utilizar uma área de preparação, o motor de sincronização permanece independente das fontes de dados ligadas e não é afetado pela sua disponibilidade e acessibilidade. Como resultado, pode processar informações de identidade a qualquer momento utilizando os dados na área de preparação. O motor de sincronização só pode solicitar as alterações eser feitas dentro da fonte de dados conectada desde que a última sessão de comunicação terminou ou apenas empurrar para fora as alterações à informação de identidade que a fonte de dados conectada ainda não recebeu, o que reduz o tráfego de rede entre o motor de sincronização e a fonte de dados conectada.

Além disso, o motor sincronizado armazena informações sobre todos os objetos que faseia no espaço do conector. Quando novos dados são recebidos, o motor de sincronização avalia sempre se os dados já foram sincronizados.

O **metaverso** é uma área de armazenamento que contém a informação de identidade agregada de múltiplas fontes de dados conectadas, fornecendo uma única visão global e integrada de todos os objetos combinados. Os objetos metaversos são criados com base na informação de identidade que é obtida a partir das fontes de dados conectadas e um conjunto de regras que permitem personalizar o processo de sincronização.

A seguinte ilustração mostra o espaço de nome do conector e o espaço de nome metaverso dentro do motor de sincronização.

![O diagrama mostra uma fonte de dados conectada e um motor de sincronização, que é separado em espaço de conector e espaços de nome metaversos, associados por uma linha chamada Connector.](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Sync objetos de identidade do motor
Os objetos no motor de sincronização são representações de ambos os objetos na fonte de dados ligada ou na visão integrada que o motor de sincronização tem desses objetos. Cada objeto do motor de sincronização deve ter um identificador globalmente único (GUID). Os GUIDs fornecem integridade dos dados e expressam relações entre objetos.

### <a name="connector-space-objects"></a>Objetos espaciais do conector
Quando o motor sincronizado comunica com uma fonte de dados conectada, lê a informação de identidade na fonte de dados conectada e utiliza essa informação para criar uma representação do objeto de identidade no espaço do conector. Não é possível criar ou eliminar estes objetos individualmente. No entanto, pode eliminar manualmente todos os objetos num espaço de conector.

Todos os objetos no espaço do conector têm dois atributos:

* Um identificador globalmente único (GUID)
* Um nome distinto (também conhecido como DN)

Se a fonte de dados ligada atribuir um atributo único ao objeto, então os objetos no espaço do conector também podem ter um atributo de âncora. O atributo âncora identifica exclusivamente um objeto na fonte de dados conectada. O motor de sincronização utiliza a âncora para localizar a representação correspondente deste objeto na fonte de dados ligada. O motor sincronizado assume que a âncora de um objeto nunca muda ao longo da vida útil do objeto.

Muitos dos Conectores usam um identificador único conhecido para gerar uma âncora automaticamente para cada objeto quando é importado. Por exemplo, o Ative Directory Connector utiliza o atributo **objectGUID** para uma âncora. Para fontes de dados conectadas que não fornecem um identificador único claramente definido, pode especificar a geração de âncora como parte da configuração do Conector.

Nesse caso, a âncora é construída a partir de um ou mais atributos únicos de um tipo de objeto, nenhum dos quais muda, e que identifica exclusivamente o objeto no espaço do conector (por exemplo, um número de funcionário ou um ID do utilizador).

Um objeto espacial de conector pode ser um dos seguintes:

* Um objeto de encenação
* Um espaço reservado

### <a name="staging-objects"></a>Objetos de encenação
Um objeto de encenação representa uma instância dos tipos de objetos designados da fonte de dados ligada. Além do GUID e do nome distinto, um objeto de encenação tem sempre um valor que indica o tipo de objeto.

Os objetos de encenação que foram importados têm sempre um valor para o atributo âncora. Os objetos de encenação que foram recentemente a provisionados pelo motor de sincronização e que estão em fase de criação na fonte de dados ligada não têm um valor para o atributo âncora.

Os objetos de encenação também carregam valores atuais de atributos de negócio, e informações operacionais necessárias pelo motor de sincronização para executar o processo de sincronização. As informações operacionais incluem bandeiras que indicam o tipo de atualizações que são encenadas no objeto de encenação. Se um objeto de encenação tiver recebido novas informações de identidade da fonte de dados ligada que ainda não foi processada, o objeto é sinalizado como **importação pendente**. Se um objeto de encenação tiver novas informações de identidade que ainda não foram exportadas para a fonte de dados ligada, está sinalizada como **exportação pendente**.

Um objeto de encenação pode ser um objeto de importação ou um objeto de exportação. O motor de sincronização cria um objeto de importação utilizando informações de objetos recebidas da fonte de dados ligada. Quando o motor de sincronização recebe informações sobre a existência de um novo objeto que corresponde a um dos tipos de objetos selecionados no Conector, cria um objeto de importação no espaço do conector como uma representação do objeto na fonte de dados conectada.

A seguinte ilustração mostra um objeto de importação que representa um objeto na fonte de dados conectada.

![O diagrama mostra um objeto de importação trazido da fonte de dados ligada para o espaço do conector no motor de sincronização.](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

O motor de sincronização cria um objeto de exportação utilizando informações de objetos no metaverso. Os objetos de exportação são exportados para a fonte de dados conectada durante a próxima sessão de comunicação. Do ponto de vista do motor de sincronização, os objetos de exportação ainda não existem na fonte de dados ligada. Por conseguinte, o atributo de âncora para um objeto de exportação não está disponível. Depois de receber o objeto do motor de sincronização, a fonte de dados conectada cria um valor único para o atributo de âncora do objeto.

A seguinte ilustração mostra como um objeto de exportação é criado usando informações de identidade no metaverso.

![O diagrama mostra um objeto de exportação trazido do metaverso para o espaço do conector e, em seguida, para a fonte de dados conectada.](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

O motor de sincronização confirma a exportação do objeto reimportando o objeto a partir da fonte de dados ligada. Os objetos de exportação tornam-se objetos de importação quando o motor de sincronização os recebe durante a próxima importação dessa fonte de dados ligada.

### <a name="placeholders"></a>Marcadores de Posição
O motor de sincronização utiliza um espaço de nome plano para armazenar objetos. No entanto, algumas fontes de dados ligadas, como o Ative Directory, utilizam um espaço hierárquico de nomes. Para transformar a informação de um espaço hierárquico em um espaço de nome plano, o motor sincronizado usa espaços reservados para preservar a hierarquia.

Cada espaço reservado representa um componente (por exemplo, uma unidade organizacional) do nome hierárquico de um objeto que não foi importado para o motor de sincronização, mas que é necessário para construir o nome hierárquico. Preenchem lacunas criadas por referências na fonte de dados ligada a objetos que não estão a encenar objetos no espaço do conector.

O motor de sincronização também utiliza espaços reservados para armazenar objetos referenciados que ainda não foram importados. Por exemplo, se a sincronização estiver configurada para incluir o atributo do gestor para o objeto *Abbie Spencer* e o valor recebido for um objeto que ainda não foi importado, tal como *CN=Lee Sperry,CN=Users,DC=fabrikam,DC=com,* a informação do gestor é armazenada como espaços reservados no espaço do conector. Se o objeto do gestor for mais tarde importado, o objeto reservado é substituído pelo objeto de encenação que representa o gestor.

### <a name="metaverse-objects"></a>Objetos metaversos
Um objeto metaverso contém a visão agregada que o motor sincronizado tem dos objetos de paragem no espaço do conector. O motor sync cria objetos metaversos utilizando a informação em objetos de importação. Vários objetos espaciais do conector podem ser ligados a um único objeto metaverso, mas um objeto espacial do conector não pode ser ligado a mais de um objeto metaverso.

Os objetos metaversos não podem ser criados manualmente ou eliminados. O motor de sincronização elimina automaticamente objetos metaversos que não têm ligação a nenhum objeto espacial do conector no espaço do conector.

Para mapear objetos dentro de uma fonte de dados conectada a um tipo de objeto correspondente dentro do metaverso, o motor de sincronização fornece um esquema extensível com um conjunto predefinido de tipos de objetos e atributos associados. Pode criar novos tipos e atributos de objetos para objetos metaversos. Os atributos podem ser de valor único ou multivalorizados, e os tipos de atributos podem ser cordas, referências, números e valores booleanos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relacionamentos entre objetos de encenação e objetos metaversos
Dentro do espaço de nome do motor sincronizado, o fluxo de dados é ativado pela relação de ligação entre objetos de paragem e objetos metaversos. Um objeto de encenação que está ligado a um objeto metaverso é chamado de **objeto unido** (ou **objeto de conector).** Um objeto de encenação que não esteja ligado a um objeto metaverso é chamado **de objeto desconectado** (ou **objeto desconexão).** Os termos aderidos e desaconses são preferidos para não confundir com os Conectores responsáveis pela importação e exportação de dados a partir de um diretório conectado.

Os espaços reservados nunca estão ligados a um objeto metaverso

Um objeto unido compreende um objeto de encenação e a sua relação ligada a um único objeto metaverso. Os objetos unidos são utilizados para sincronizar valores de atributos entre um objeto de espaço de conector e um objeto metaverso.

Quando um objeto de encenação se torna um objeto unido durante a sincronização, os atributos podem fluir entre o objeto de encenação e o objeto metaverso. O fluxo de atributos é bidirecional e é configurado utilizando regras de atributos de importação e regras de atributos de exportação.

Um único objeto espacial do conector pode ser ligado a apenas um objeto metaverso. No entanto, cada objeto metaverso pode ser ligado a múltiplos objetos espaciais de conector no mesmo ou em diferentes espaços de conector, como mostra a seguinte ilustração.

![O diagrama mostra dois objetos de dados ligados associados pelos conectores a um motor de sincronização, que uniu objetos e um objeto desacoplado.](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

A relação ligada entre o objeto de encenação e um objeto metaverso é persistente e só pode ser removida por regras que especifique.

Um objeto desatado é um objeto de encenação que não está ligado a nenhum objeto metaverso. Os valores de atributo de um objeto não associado não são processados mais dentro do metaverso. Os valores de atributo do objeto correspondente na fonte de dados ligada não são atualizados pelo motor de sincronização.

Ao utilizar objetos desacoplados, pode armazenar informações de identidade no motor de sincronização e processá-la mais tarde. Manter um objeto de encenação como um objeto desafetado no espaço do conector tem muitas vantagens. Como o sistema já encenou as informações necessárias sobre este objeto, não é necessário voltar a criar uma representação deste objeto durante a próxima importação da fonte de dados conectada. Desta forma, o motor de sincronização tem sempre uma imagem completa da fonte de dados ligada, mesmo que não exista uma ligação atual à fonte de dados ligada. Os objetos desunião podem ser convertidos em objetos unidos e vice-versa, dependendo das regras que especifica.

Um objeto importado é criado como um objeto desafetado. Um objeto de exportação deve ser um objeto associado. A lógica do sistema aplica esta regra e elimina todos os objetos de exportação que não são um objeto associado.

## <a name="sync-engine-identity-management-process"></a>Processo de gestão da identidade do motor sincronizado
O processo de gestão de identidade controla a forma como a informação de identidade é atualizada entre diferentes fontes de dados conectadas. A gestão da identidade ocorre em três processos:

* Importar
* Sincronização
* Exportar

Durante o processo de importação, o motor sincronizado avalia a informação de identidade recebida a partir de uma fonte de dados conectada. Quando as alterações são detetadas, cria novos objetos de paragem ou atualiza os objetos de paragem existentes no espaço do conector para sincronização.

Durante o processo de sincronização, o motor sincronizado atualiza o metaverso para refletir as alterações que ocorreram no espaço do conector e atualiza o espaço do conector para refletir as alterações que ocorreram no metaverso.

Durante o processo de exportação, o motor de sincronização empurra para fora as alterações que são encenadas em objetos de paragem e que são sinalizadas como exportação pendente.

A seguinte ilustração mostra onde cada um dos processos ocorre à medida que a informação de identidade flui de uma fonte de dados conectada para outra.

![O diagrama mostra o fluxo de informações de identidade de dados conectados ao espaço do conector (importação) ao metaverso ao espaço do conector (sincronização) até aos dados conectados (exportação).](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Processo de importação
Durante o processo de importação, o motor de sincronização avalia as atualizações à informação de identidade. O motor de sincronização compara as informações de identidade recebidas da fonte de dados conectada com a informação de identidade sobre um objeto de encenação e determina se o objeto de encenação requer atualizações. Se for necessário atualizar o objeto de encenação com novos dados, o objeto de encenação é sinalizado como importação pendente.

Ao antever objetos no espaço do conector antes da sincronização, o motor de sincronização só pode processar a informação de identidade que mudou. Este processo proporciona os seguintes benefícios:

* **Sincronização eficiente.** A quantidade de dados processados durante a sincronização é minimizada.
* **Ressincronização eficiente**. Pode alterar a forma como o motor sincroniza as informações de identidade sem voltar a ligar o motor de sincronização à fonte de dados.
* **Oportunidade de pré-visualização da sincronização.** Pode pré-visualizar a sincronização para verificar se as suas suposições sobre o processo de gestão de identidade estão corretas.

Para cada objeto especificado no Conector, o motor de sincronização tenta primeiro localizar uma representação do objeto no espaço do conector do Conector. O motor Sync examina todos os objetos de paragem no espaço do conector e tenta encontrar um objeto de encenação correspondente que tenha um atributo de âncora correspondente. Se nenhum objeto de encenação existente tiver um atributo de âncora correspondente, o motor sincronizado tenta encontrar um objeto de encenação correspondente com o mesmo nome distinto.

Quando o motor de sincronização encontra um objeto de encenação que corresponde a um nome distinto, mas não por âncora, ocorre o seguinte comportamento especial:

* Se o objeto localizado no espaço do conector não tiver âncora, o motor sincronizado remove este objeto do espaço do conector e marca o objeto metaverso a que está ligado como **redator no próximo funcionamento da sincronização**. Depois cria o novo objeto de importação.
* Se o objeto localizado no espaço do conector tiver uma âncora, então o motor sincronizado pressupõe que este objeto foi renomeado ou eliminado no diretório ligado. Atribui um novo nome temporário e distinto para o objeto do espaço do conector para que possa encenar o objeto de entrada. O objeto antigo torna-se então **transitório,** esperando que o Conector importe o rebatizador ou a supressão para resolver a situação.

Se o motor de sincronização localizar um objeto de paragem que corresponda ao objeto especificado no Conector, determina que tipo de alterações a aplicar. Por exemplo, o motor de sincronização pode mudar o nome ou apagar o objeto na fonte de dados conectada, ou pode apenas atualizar os valores de atributos do objeto.

Os objetos de encenação com dados atualizados são marcados como importação pendente. Existem diferentes tipos de importações pendentes. Dependendo do resultado do processo de importação, um objeto de encenação no espaço do conector tem um dos seguintes tipos de importação pendentes:

* **Nenhuma.** Não estão disponíveis alterações em nenhum dos atributos do objeto de encenação. O motor de sincronização não sinaliza este tipo como importação pendente.
* **Adicione**. O objeto de encenação é um novo objeto de importação no espaço do conector. Sync motor sinaliza este tipo como importação pendente para processamento adicional no metaverso.
* **Atualização**. O motor sync encontra um objeto de encenação correspondente no espaço do conector e sinaliza este tipo como importação pendente para que as atualizações aos atributos possam ser processadas no metaverso. As atualizações incluem renomeação de objetos.
* **Eliminar**. O motor sync encontra um objeto de paragem correspondente no espaço do conector e sinaliza este tipo como importação pendente para que o objeto de união possa ser eliminado.
* **Eliminar/Adicionar**. O motor sync encontra um objeto de paragem correspondente no espaço do conector, mas os tipos de objetos não correspondem. Neste caso, é encenada uma modificação de exclusão. Uma modificação de exclusão indica ao motor de sincronização que deve ocorrer uma ressincronização completa deste objeto porque diferentes conjuntos de regras se aplicam a este objeto quando o tipo de objeto muda.

Ao definir o estado de importação pendente de um objeto de encenação, é possível reduzir significativamente a quantidade de dados processados durante a sincronização porque assim permite que o sistema processe apenas os objetos que tenham dados atualizados.

### <a name="synchronization-process"></a>Processo de sincronização
A sincronização consiste em dois processos relacionados:

* Sincronização de entrada, quando o conteúdo do metaverso é atualizado utilizando os dados no espaço do conector.
* Sincronização de saída, quando o conteúdo do espaço do conector é atualizado utilizando dados no metaverso.

Utilizando as informações encenadas no espaço do conector, o processo de sincronização de entrada cria no metaverso a visão integrada dos dados que são armazenados nas fontes de dados ligadas. Ou todos os objetos de encenação ou apenas aqueles com informações de importação pendentes são agregados, dependendo da configuração das regras.

O processo de sincronização de saída atualiza os objetos de exportação quando os objetos metaversos mudam.

A sincronização de entrada cria a visão integrada no metaverso da informação de identidade que é recebida a partir das fontes de dados conectadas. O motor sync pode processar informações de identidade a qualquer momento utilizando as informações de identidade mais recentes que tem a partir da fonte de dados conectada.

**Sincronização de entrada**

A sincronização de entrada inclui os seguintes processos:

* **Provisão** (também chamada **de Projeção** se for importante distinguir este processo do provisionamento de sincronização de saída). O motor Sync cria um novo objeto metaverso baseado num objeto de paragem e liga-os. A provisão é uma operação ao nível de objetos.
* **Junte-se** a nós . O motor Sync liga um objeto de encenação a um objeto metaverso existente. Uma junção é uma operação ao nível de objetos.
* **Fluxo de atributos de importação**. O motor sincroniza os valores do atributo, chamado fluxo de atributos, do objeto no metaverso. O fluxo de atributos de importação é uma operação de nível de atributo que requer uma ligação entre um objeto de encenação e um objeto metaverso.

A provisão é o único processo que cria objetos no metaverso. A disposição afeta apenas objetos de importação que sejam objetos desunião. Durante a provisão, o motor de sincronização cria um objeto metaverso que corresponde ao tipo de objeto do objeto de importação e estabelece uma ligação entre ambos os objetos, criando assim um objeto unido.

O processo de junção também estabelece uma ligação entre objetos de importação e um objeto metaverso. A diferença entre a junção e a disposição é que o processo de junção requer que o objeto de importação esteja ligado a um objeto metaverso existente, onde o processo de fornecimento cria um novo objeto metaverso.

O motor de sincronização tenta juntar um objeto de importação a um objeto metaverso utilizando critérios especificados na configuração da Regra de Sincronização.

Durante a provisão e adere aos processos, o motor sincronizado liga um objeto desacoplado a um objeto metaverso, fazendo com que se juntem. Após a conclusão destas operações ao nível do objeto, o motor de sincronização pode atualizar os valores de atributo do objeto metaverso associado. Este processo chama-se fluxo de atributos de importação.

O fluxo de atributos de importação ocorre em todos os objetos de importação que transportam novos dados e estão ligados a um objeto metaverso.

**Sincronização de saída**

A sincronização de saída atualiza os objetos de exportação quando um objeto metaverso muda, mas não é eliminado. O objetivo da sincronização de saída é avaliar se as alterações nos objetos metaversos requerem atualizações para a paragem de objetos nos espaços do conector. Em alguns casos, as alterações podem exigir que os objetos de paragem em todos os espaços do conector sejam atualizados. Os objetos de encenação que são alterados são sinalizados como exportação pendente, fazendo com que exportem objetos. Estes objetos de exportação são posteriormente empurrados para a fonte de dados ligada durante o processo de exportação.

A sincronização de saída tem três processos:

* **Aprovisionamento**
* **Deprovisionamento**
* **Fluxo de atributos de exportação**

O provisionamento e a desprovisionamento são operações ao nível dos objetos. A desprovisionamento depende do provisionamento, pois só o provisionamento pode iniciá-lo. A desprovisionamento é desencadeada quando o provisionamento remove a ligação entre um objeto metaverso e um objeto de exportação.

O provisionamento é sempre ativado quando são aplicadas alterações a objetos no metaverso. Quando são feitas alterações em objetos metaversos, o motor de sincronização pode executar qualquer uma das seguintes tarefas como parte do processo de provisionamento:

* Crie objetos unidos, onde um objeto metaverso está ligado a um objeto de exportação recém-criado.
* Mude o nome de um objeto associado.
* Desarmem ligações entre um objeto metaverso e objetos de encenação, criando um objeto desaderido.

Se o provisionamento requer que o motor de sincronização crie um novo objeto de conector, o objeto de paragem ao qual o objeto metaverso está ligado é sempre um objeto de exportação, porque o objeto ainda não existe na fonte de dados ligada.

Se o provisionamento requer que o motor de sincronização se junte a um objeto associado, criando um objeto desacoplado, é ativado a desprovisionamento. O processo de desprovisionamento elimina o objeto.

Durante a desprovisionamento, a eliminação de um objeto de exportação não apaga fisicamente o objeto. O objeto é **sinalizado** como eliminado , o que significa que a operação de eliminação é encenada no objeto.

O fluxo de atributos de exportação também ocorre durante o processo de sincronização de saída, semelhante à forma como o fluxo de atributos de importação ocorre durante a sincronização de entrada. O fluxo de atributos de exportação ocorre apenas entre metaversos e objetos de exportação que se juntam.

### <a name="export-process"></a>Processo de exportação
Durante o processo de exportação, o motor de sincronização examina todos os objetos de exportação que estão sinalizados como exportação pendente no espaço do conector e, em seguida, envia atualizações para a fonte de dados conectada.

O motor de sincronização pode determinar o sucesso de uma exportação, mas não pode determinar suficientemente que o processo de gestão da identidade esteja concluído. Os objetos na fonte de dados ligada podem sempre ser alterados por outros processos. Uma vez que o motor de sincronização não tem uma ligação persistente à fonte de dados ligada, não é suficiente fazer suposições sobre as propriedades de um objeto na fonte de dados ligada apenas com base numa notificação de exportação bem sucedida.

Por exemplo, um processo na fonte de dados conectada poderia alterar os atributos do objeto de volta aos seus valores originais (ou seja, a fonte de dados conectada poderia substituir os valores imediatamente após os dados forem empurrados para fora pelo motor de sincronização e aplicados com sucesso na fonte de dados conectada).

O motor de sincronização armazena informações sobre o estado de exportação e importação sobre cada objeto de encenação. Se os valores dos atributos especificados na lista de inclusão de atributos tiverem mudado desde a última exportação, a armazenagem do estatuto de importação e exportação permite que o motor de sincronização reaja adequadamente. O motor sync utiliza o processo de importação para confirmar valores de atributos que foram exportados para a fonte de dados conectada. Uma comparação entre as informações importadas e as exportadas, tal como demonstra a seguinte ilustração, permite que o motor de sincronização determine se a exportação foi bem sucedida ou se precisa de ser repetida.

![O diagrama mostra a sincronização de um objeto entre o espaço do conector e os dados ligados sobre o conector.](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Por exemplo, se as exportações de motores de sincronização atribuem C, que tem um valor de 5, a uma fonte de dados ligada, armazena C=5 na sua memória de estado de exportação. Cada exportação adicional deste objeto resulta numa tentativa de exportar C=5 para a fonte de dados ligada novamente porque o motor de sincronização pressupõe que este valor não tenha sido aplicado persistentemente ao objeto (isto é, a menos que um valor diferente tenha sido importado recentemente da fonte de dados ligada). A memória de exportação é apagada quando C=5 é recebida durante uma operação de importação no objeto.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração da [sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

