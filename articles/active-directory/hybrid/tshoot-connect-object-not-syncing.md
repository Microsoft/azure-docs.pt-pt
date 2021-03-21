---
title: Resolva um objeto que não está sincronizado com o Azure Ative Directory | Microsoft Docs'
description: Resolva um objeto que não está a sincronizar com o Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413467"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Resolução de problemas um objeto que não está sincronizado com o Azure Ative Directory

Se um objeto não estiver a sincronizar como esperado com o Microsoft Azure Ative Directory (Azure AD), pode ser devido a várias razões. Se recebeu um e-mail de erro da Azure AD ou se vir o erro no Azure AD Connect Health, leia [os erros de resolução de problemas durante a sincronização.](tshoot-connect-sync-errors.md) Mas se está a resolver um problema em que o objeto não está no AZure AD, este artigo é para si. Descreve como encontrar erros no componente Azure AD Connect sincronização.

>[!IMPORTANT]
>Para a Azure AD Ligar a implementação com a versão 1.1.749.0 ou superior, utilize a [tarefa de resolução de problemas](tshoot-connect-objectsync.md) no assistente para resolver problemas de sincronização de objetos. 

## <a name="synchronization-process"></a>Processo de sincronização

Antes de investigarmos problemas de sincronização, vamos entender o processo de sincronização do Azure AD Connect:

  ![Diagrama do processo de sincronização Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço do conector, uma tabela numa base de dados
* **MV:** Metaverso, uma tabela numa base de dados

### <a name="synchronization-steps"></a>**Etapas de sincronização**
O processo de sincronização envolve os seguintes passos:

1. **Importação a partir de AD:** Os objetos do Diretório Ativo são trazidos para o CS do Diretório Ativo.

2. **Importação da Azure AD:** Os objetos AD AD do Azure são trazidos para o CS Azure AD.

3. **Sincronização:** As regras de sincronização de entrada e as regras de sincronização de saída são executadas na ordem do número de precedência, de baixo para mais alto. Para ver as regras de sincronização, vá ao Editor de Regras de Sincronização a partir das aplicações para desktop. As regras de sincronização de entrada trazem dados de CS para MV. As regras de sincronização de saída movem os dados de MV para CS.

4. **Exportação para AD:** Após a sincronização, os objetos são exportados do Ative Directory CS para o Ative Directory.

5. **Exportação para Azure AD:** Após a sincronização, os objetos são exportados do Azure AD CS para Azure AD.

## <a name="troubleshooting"></a>Resolução de problemas

Para encontrar os erros, olhe para alguns lugares diferentes, na seguinte ordem:

1. Os [registos de operação](#operations) para encontrar erros identificados pelo motor de sincronização durante a importação e sincronização.
2. O [espaço do conector](#connector-space-object-properties) para encontrar objetos em falta e erros de sincronização.
3. O [metaverso](#metaverse-object-properties) para encontrar problemas relacionados com dados.

Inicie [o Gestor de Serviços de Sincronização](how-to-connect-sync-service-manager-ui.md) antes de iniciar estes passos.

## <a name="operations"></a>Operações
O separador **Operações** no Gestor de Serviços de Sincronização é onde deve iniciar a resolução de problemas. Este separador mostra os resultados das operações mais recentes. 

![Screenshot do Gestor de Serviço de Sincronização, mostrando separador de operações selecionado](./media/tshoot-connect-object-not-syncing/operations.png)  

A metade superior do separador **Operações** mostra tudo funciona por ordem cronológica. Por predefinição, o registo de operações mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [programador](how-to-connect-sync-feature-scheduler.md). Procure por qualquer corrida que não mostre um estado de **sucesso.** Pode alterar a triagem clicando nos cabeçalhos.

A coluna **Status** contém as informações mais importantes e mostra o problema mais grave para uma corrida. Aqui está um resumo rápido dos estatutos mais comuns por ordem de prioridade de investigação (onde * indica várias possíveis cordas de erro).

| Estado | Comentário |
| --- | --- |
| parado-* |A corrida não terminou. Isto pode acontecer, por exemplo, se o sistema remoto estiver em avaria e não puder ser contactado. |
| limite de erro parada |Há mais de 5.000 erros. A execução foi interrompida automaticamente devido ao grande número de erros. |
| \*concluídos- erros |A corrida terminou, mas há erros (menos de 5.000) que devem ser investigados. |
| concluído- \* avisos |A execução terminou, mas alguns dados não estão no estado esperado. Se tiver erros, esta mensagem geralmente é apenas um sintoma. Não investigue avisos até ter abordado erros. |
| exito |Sem problemas. |

Quando seleciona uma linha, a parte inferior do separador **Operações** é atualizada para mostrar os detalhes dessa execução. No lado extremo esquerdo desta área, você pode ter uma lista intitulada **Step #**. Esta lista só aparece se tiver vários domínios na sua floresta e cada domínio for representado por um passo. O nome de domínio pode ser encontrado sob o título **Partição**. No âmbito do título de Estatísticas da **Sincronização,** pode encontrar mais informações sobre o número de alterações que foram processadas. Selecione os links para obter uma lista dos objetos alterados. Se tiver objetos com erros, esses erros aparecem no título **Desincronização.**

### <a name="errors-on-the-operations-tab"></a>Erros no separador Operações
Quando tem erros, o Gestor de Serviço de Sincronização mostra tanto o objeto em erro como o próprio erro como links que fornecem mais informações.

![Screenshot de erros no Gestor de Serviços de Sincronização](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Comece por selecionar a cadeia de erros. (Na figura anterior, a cadeia de erro é **sincronizada-erro-função desencadeada**.) É apresentado pela primeira vez com uma visão geral do objeto. Para ver o erro real, selecione **Stack Trace**. Este vestígio fornece informações de nível de depurado para o erro.

Clique com o botão direito na caixa **de informações de 'Chamada Stack',** clique **em Selecionar Tudo** e, em seguida, selecione **Copy**. Em seguida, copie a pilha e veja o erro no seu editor favorito, como o Notepad.

Se o erro for do **SyncRulesEngine,** a informação da stack de chamadas lista primeiro todos os atributos do objeto. Desloque-se para baixo até ver o título **InnerException =>**.  

  ![Screenshot do Gestor de Serviço de Sincronização, mostrando informações de erro sob a rubrica InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
A linha após a posição mostra o erro. Na figura anterior, o erro provém de uma regra de sincronização personalizada que Fabrikam criou.

Se o erro não der informações suficientes, é hora de olhar para os dados em si. Selecione a ligação com o identificador de objetos e continue a resolver problemas com o [objeto importado do espaço do conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propriedades dos objetos de espaço conector
Se o separador [**Operações**](#operations) não apresentar erros, siga o objeto espacial do conector do Ative Directory para o metaverso até a Azure AD. Neste caminho, deve descobrir onde está o problema.

### <a name="searching-for-an-object-in-the-cs"></a>À procura de um objeto no CS

No Gestor de Serviço de Sincronização, selecione **Connectors**, selecione o Conector de Diretório Ativo e selecione **o Espaço do Conector de Busca**.

Na caixa **Scope,** selecione **RDN** quando pretender pesquisar no atributo CN, ou selecione **DN ou âncora** quando pretender pesquisar o atributo Nome **distinto.** Introduza um valor e selecione **Pesquisar**. 
 
![Screenshot de uma pesquisa espacial do conector](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Se não encontrar o objeto que procura, pode ter sido filtrado com [filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ou [filtragem baseada em OU.](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) Para verificar se a filtragem está configurada como esperado, leia [a sincronização Azure AD Connect: Filtragem de configuração](how-to-connect-sync-configure-filtering.md).

Pode efetuar outra pesquisa útil selecionando o Conector AD Azure. Na caixa **Scope,** selecione **Import Pendente** e, em seguida, selecione a caixa de verificação **Add.** Esta pesquisa dá-lhe todos os objetos sincronizados em Azure AD que não podem ser associados a um objeto no local.  

![Screenshot de órfãos em uma pesquisa espacial de conector](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Esses objetos foram criados por outro motor de sincronização ou por um motor de sincronização com uma configuração de filtragem diferente. Estes objetos órfãos já não são geridos. Reveja esta lista e considere remover estes objetos utilizando os cmdlets [Azure AD PowerShell.](/previous-versions/azure/jj151815(v=azure.100))

### <a name="cs-import"></a>Importação de CS
Quando abre um objeto CS, existem vários separadores no topo. O **separador Import** mostra os dados que são apresentados após uma importação.  

![Screenshot da janela Object Properties do Objeto de Espaço do Conector, com o separador Import selecionado](./media/tshoot-connect-object-not-syncing/csobject.png)    

A coluna **Old Value** mostra o que está atualmente armazenado no Connect, e a coluna **New Value** mostra o que foi recebido do sistema de origem e ainda não foi aplicado. Se houver um erro no objeto, as alterações não são processadas.

O **separador Error de sincronização** só é visível na janela **"Propriedades do Objeto do Espaço do Conector"** se houver um problema com o objeto. Para obter mais informações, reveja como [resolver erros de sincronização no separador **Operações.**](#errors-on-the-operations-tab)

![Screenshot do separador Erro de Sincronização na janela "Propriedades do Objeto espacial do Conector"](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Linhagem CS
O **separador lineage** na janela **Connector Space Object Properties** mostra como o objeto de espaço do conector está relacionado com o objeto metaverso. Pode ver quando o conector importou pela última vez uma alteração do sistema ligado e quais as regras aplicadas para preencher dados no metaverso.  

![Screenshot mostrando o separador lineage na janela Object Properties do Objeto de Espaço do Conector](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na figura anterior, a coluna **Ação** apresenta uma regra de sincronização de entrada com a **disposição de** ação . Isto indica que enquanto este objeto espacial do conector estiver presente, o objeto metaverso permanece. Se a lista de regras de sincronização mostrar uma regra de sincronização de saída com uma ação **de Provisão,** este objeto é eliminado quando o objeto metaverso é eliminado.  

![Screenshot de uma janela de linhagem no separador lineage na janela Connector Space Object Properties](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na figura anterior, também pode ver na coluna **PasswordSync** que o espaço do conector de entrada pode contribuir com alterações na palavra-passe uma vez que uma regra de sincronização tem o valor **Verdadeiro**. Esta palavra-passe é enviada para Azure AD através da regra de saída.

A partir do **separador Linhagem,** pode chegar ao metaverso selecionando [**Metaverse Object Properties**](#mv-attributes).

### <a name="preview"></a>Pré-visualizar
No canto inferior esquerdo da janela **Connector Space Object Properties** encontra-se o botão **de pré-visualização.** Selecione este botão para abrir a página **de pré-visualização,** onde pode sincronizar um único objeto. Esta página é útil se estiver a resolver algumas regras de sincronização personalizadas e quiser ver o efeito de uma mudança num único objeto. Pode selecionar uma **sincronização Completa** ou uma **sincronização Delta.** Também pode selecionar **''' ' 'Gerar Pré-visualização',** que apenas mantém a alteração na memória. Ou **selecione 'Commit Preview',** que atualiza o metaverso e encena todas as alterações nos espaços de conector alvo.  

![Screenshot da página de pré-visualização, com pré-visualização de início selecionado](./media/tshoot-connect-object-not-syncing/preview.png)  

Na pré-visualização pode inspecionar o objeto e ver qual a regra aplicada para um determinado fluxo de atributos.  

![Screenshot da página de pré-visualização, mostrando Import Attribute Flow](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Registo
Ao lado do botão **Pré-visualização,** selecione o botão **Registar** para abrir a página **'Registar'.** Aqui pode ver o estado e o histórico da sincronização da palavra-passe. Para obter mais informações, consulte [a sincronização de hash de palavra-passe de resolução de problemas com a sincronização Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriedades de objeto metaverso
Normalmente é melhor começar a procurar a partir do espaço do conector ative de origem. Mas também pode começar a procurar a partir do metaverso.

### <a name="searching-for-an-object-in-the-mv"></a>À procura de um objeto no MV
No Gestor de Serviço de Sincronização, selecione **Metaverse Search,** como na seguinte figura. Crie uma consulta que sabe que encontra o utilizador. Procure por atributos comuns, tais como **o nome** de conta **(sAMAccountName)** e **o nome do utilizadorPrincipalName**. Para mais informações, consulte [a pesquisa do Sync Service Manager Metaverse.](how-to-connect-sync-service-manager-ui-mvsearch.md)

![Screenshot do Gestor de Serviço de Sincronização, com o separador Metaverse Search selecionado](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Na janela **Resultados de Pesquisa,** clique no objeto.

Se não encontrou o objeto, ainda não chegou ao metaverso. Continue a procurar o objeto no espaço do conector Ative [Directory](#connector-space-object-properties). Se encontrar o objeto no espaço do conector ative, pode haver um erro de sincronização que está a bloquear o objeto de chegar ao metaverso, ou pode ser aplicado um filtro de deteção de regras de sincronização.

### <a name="object-not-found-in-the-mv"></a>Objeto não encontrado no MV
Se o objeto estiver no CS do Diretório Ativo mas não estiver presente no MV, é aplicado um filtro de deteção. Para ver o filtro de deteção, vá ao menu de aplicações para desktop e selecione **Synchronization Rules Editor**. Filtrar as regras aplicáveis ao objeto ajustando o filtro abaixo.

  ![Screenshot do Synchronization Rules Editor, mostrando uma pesquisa de regras de sincronização de entrada](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Veja cada regra na lista de cima e verifique o **filtro De scoping**. No filtro de deteção a seguir, se o valor **isCriticalSystemObject** for nulo ou FALSO ou vazio, está no âmbito.

  ![Screenshot de um filtro de deteção em uma pesquisa de regra de sincronização de entrada](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Vá à lista de [atributos CS Import](#cs-import) e verifique qual o filtro que está a bloquear a deslocação do objeto para o MV. A lista de atributos **do Espaço Do Conector** mostrará apenas atributos não nulos e não vazios. Por exemplo, se **o IsCriticalSystemObject** não aparecer na lista, o valor deste atributo é nulo ou vazio.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objeto não encontrado no Azure AD CS
Se o objeto não estiver presente no espaço de conector do Azure AD mas estiver presente no MV, olhe para o filtro de escotagem das regras de saída do espaço do conector correspondente e descubra se o objeto é filtrado porque os [atributos MV](#mv-attributes) não satisfazem os critérios.

Para ver o filtro de deteção de saída, selecione as regras aplicáveis para o objeto ajustando o filtro abaixo. Ver cada regra e olhar para o valor [do atributo MV](#mv-attributes) correspondente.

  ![Screenshot de uma pesquisa de regras de sincronização de saída no Synchronization Rules Editor](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atributos MV
No separador **Atributos,** pode ver os valores e quais os conectores que os contribuíram.  

![Screenshot da janela Metaverse Object Properties, com o separador Atributos selecionado](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Se um objeto não estiver a sincronizar, faça as seguintes perguntas sobre os estados de atributos no metaverso:
- O atributo **cloudFiltered** está presente e definido para **Verdadeiro?** Se for, foi filtrado de acordo com os passos da [filtragem baseada](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)em atributos .
- A fonte **de atributosAnchor está** presente? Caso contrário, tem uma topologia florestal de recursos de conta? Se um objeto for identificado como uma caixa de correio ligada (o atributo **msExchRecipientTypeDetails** tem o valor **2),** a **fonteAnchor** é contribuída pela floresta com uma conta de Diretório Ativo ativada. Certifique-se de que a conta principal foi importada e sincronizada corretamente. A conta principal deve ser listada entre os [conectores](#mv-connectors) do objeto.

### <a name="mv-connectors"></a>Conectores MV
O **separador Conectores** mostra todos os espaços de conector que têm uma representação do objeto. 
 
![Screenshot da janela Metaverse Object Properties, com o separador Conectores selecionado](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Deve ter um conector para:

- Cada floresta ative diretório em que o utilizador está representado. Esta representação pode incluir **objetos de Segurança** e **Contactos** estrangeiros.
- Um conector em Azure AD.

Se faltar o conector ao Azure AD, reveja a secção nos [atributos MV](#mv-attributes) para verificar os critérios de provisionamento ao Azure AD.

A partir do **separador Conectores** também pode ir ao objeto espacial do [conector.](#connector-space-object-properties) Selecione uma linha e clique em **Propriedades**.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a sincronização Azure AD Connect](how-to-connect-sync-whatis.md).
- Saiba mais sobre [identidade híbrida.](whatis-hybrid-identity.md)