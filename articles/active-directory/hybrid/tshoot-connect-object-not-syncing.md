---
title: Problemas de resolução de um objeto que não está a sincronizar com o Diretório Ativo do Azure [ Microsoft Docs'
description: Problemas de resolução de um objeto que não está a sincronizar com o Diretório Ativo Azure.
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
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253536"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Problemas de resolução de um objeto que não está sincronizando com O Diretório Ativo Azure

Se um objeto não estiver a sincronizar como esperado com o Microsoft Azure Ative Directory (Azure AD), pode ser devido a várias razões. Se recebeu um e-mail de erro da Azure AD ou se vir o erro na Azure AD Connect Health, leia erros de resolução de problemas durante a [sincronização.](tshoot-connect-sync-errors.md) Mas se está a resolver um problema em que o objeto não está em Azure AD, este artigo é para si. Descreve como encontrar erros na sincronização do componente azure AD Connect no local.

>[!IMPORTANT]
>Para a implementação do Azure AD Connect com a versão 1.1.749.0 ou superior, utilize a tarefa de resolução de [problemas](tshoot-connect-objectsync.md) no assistente para resolver problemas de sincronização de objetos. 

## <a name="synchronization-process"></a>Processo de sincronização

Antes de investigarmos questões de sincronização, vamos entender o processo de sincronização do Azure AD Connect:

  ![Diagrama do processo de sincronização azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço conector, uma tabela numa base de dados
* **MV:** Metaverso, uma tabela numa base de dados

### <a name="synchronization-steps"></a>**Etapas de sincronização**
O processo de sincronização envolve seguir etapas:

1. **Importação de AD:** Os objetos de Diretório Ativo são trazidos para o Diretório Ativo CS.

2. **Importação a partir de Azure AD:** Os objetos Azure AD são trazidos para o CS AD Azure.

3. **Sincronização:** As regras de sincronização de entrada e as regras de sincronização de saída são executadas na ordem do número de precedência, de baixo para superior. Para ver as regras de sincronização, vá ao Editor de Regras de Sincronização a partir das aplicações de desktop. As regras de sincronização de entrada trazem dados de CS para MV. As regras de sincronização de saída movem dados de MV para CS.

4. **Exportação para AD:** Após a sincronização, os objetos são exportados do Diretório Ativo CS para o Diretório Ativo.

5. **Exportação para AD Azure:** Após a sincronização, os objetos são exportados do CS Azure AD para O D.Azure.

## <a name="troubleshooting"></a>Resolução de problemas

Para encontrar os erros, olhe para alguns lugares diferentes, na seguinte ordem:

1. Os registos de [funcionamento](#operations) para encontrar erros identificados pelo motor de sincronização durante a importação e sincronização.
2. O [espaço do conector](#connector-space-object-properties) para encontrar objetos em falta e erros de sincronização.
3. O [metaverso](#metaverse-object-properties) para encontrar problemas relacionados com dados.

Inicie [o Gestor de Serviços de Sincronização](how-to-connect-sync-service-manager-ui.md) antes de iniciar estes passos.

## <a name="operations"></a>Operações
O separador **Operações** no Gestor de Serviços de Sincronização é onde deve iniciar a sua resolução de problemas. Este separador mostra os resultados das operações mais recentes. 

![Screenshot do Gestor de Serviçode Sincronização, mostrando o separador Operações selecionado](./media/tshoot-connect-object-not-syncing/operations.png)  

A metade superior do separador **Operações** mostra que tudo corre por ordem cronológica. Por predefinição, o registo de operações mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [programador](how-to-connect-sync-feature-scheduler.md). Procure qualquer corrida que não mostre um estatuto de **sucesso.** Pode alterar a classificação clicando nos cabeçalhos.

A coluna **Status** contém as informações mais importantes e mostra o problema mais grave para uma corrida. Aqui está um resumo rápido dos estatutos mais comuns por ordem de prioridade de investigação (onde * indica várias possíveis cadeias de erro).

| Estado | Comentário |
| --- | --- |
| parou-* |A corrida não podia terminar. Isto pode acontecer, por exemplo, se o sistema remoto estiver avariado e não puder ser contactado. |
| parou-erro-limite |Há mais de 5.000 erros. A corrida foi automaticamente interrompida devido ao grande número de erros. |
| concluídos-\*-erros |A corrida terminou, mas há erros (menos de 5.000) que devem ser investigados. |
| concluídas-\*-avisos |A corrida terminou, mas alguns dados não estão no estado esperado. Se tiver erros, esta mensagem geralmente é apenas um sintoma. Não investigue avisos até ter resolvido erros. |
| exito |Sem problemas. |

Quando selecionar uma linha, a parte inferior do separador **Operações** é atualizada para mostrar os detalhes dessa execução. No lado mais à esquerda desta área, você pode ter uma lista intitulada **Step #**. Esta lista só aparece se tiver vários domínios na sua floresta e cada domínio for representado por um passo. O nome de domínio pode ser encontrado na rubrica **Partição**. No âmbito da rubrica Estatísticas de **Sincronização,** pode encontrar mais informações sobre o número de alterações que foram processadas. Selecione os links para obter uma lista dos objetos alterados. Se tiver objetos com erros, esses erros aparecem sob a rubrica Erros de **Sincronização.**

### <a name="errors-on-the-operations-tab"></a>Erros no separador Operações
Quando tem erros, o Gestor de Serviços de Sincronização mostra tanto o objeto em erro como o próprio erro como links que fornecem mais informações.

![Screenshot de erros no Gestor de Serviços de Sincronização](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Comece por selecionar a cadeia de erro. (Na figura anterior, a cadeia de erro é **ativada por funções de erro de sincronização**.) É apresentado pela primeira vez com uma visão geral do objeto. Para ver o erro real, selecione **Stack Trace**. Este vestígio fornece informações de nível de depuração para o erro.

Clique na caixa de **informação** de pilhas de chamadas, clique em **Selecionar Tudo,** e depois selecione **Copiar**. Em seguida, copie a pilha e veja o erro no seu editor favorito, como o Notepad.

Se o erro for do **SyncRulesEngine,** a informação da pilha de chamadas lista primeiro todos os atributos do objeto. Desloque-se para baixo até ver a rubrica **InnerException =>**.  

  ![Screenshot do Gestor de Serviço de Sincronização, mostrando informações de erro sob a rubrica InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
A linha após a direção mostra o erro. Na figura anterior, o erro é de uma regra de sincronização personalizada que Fabrikam criou.

Se o erro não der informações suficientes, é hora de olhar para os próprios dados. Selecione a ligação com o identificador de objetos e continue a resolver problemas com o objeto importado do [espaço do conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propriedades dos objetos de espaço conector
Se o separador [**Operações**](#operations) não apresentar erros, siga o objeto espacial do conector do Diretório Ativo para o metaverso para o AD Azure. Neste caminho, deve encontrar onde está o problema.

### <a name="searching-for-an-object-in-the-cs"></a>À procura de um objeto no CS

No Gestor de Serviços de Sincronização, selecione **Conectores,** selecione o Conector de Diretório Ativo e selecione **Search Connector Space**.

Na caixa **Scope,** selecione **RDN** quando pretender pesquisar o atributo CN, ou selecionar **DN ou âncora** quando pretender pesquisar o **atributo distinto do Nome.** Introduza um valor e selecione **Procurar**. 
 
![Screenshot de uma pesquisa espacial conector](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Se não encontrar o objeto que procura, pode ter sido filtrado com [filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ou [filtragem baseada em OU](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Para verificar se a filtragem está configurada como esperado, leia o [sincronizado Azure AD Connect: Configure a filtragem](how-to-connect-sync-configure-filtering.md).

Pode efetuar outra pesquisa útil selecionando o Conector AD Azure. Na caixa **Scope,** selecione **Pendente import,** e, em seguida, selecione a caixa de verificação **Adicionar.** Esta pesquisa dá-lhe todos os objetos sincronizados em Azure AD que não podem ser associados a um objeto no local.  

![Screenshot de órfãos em uma pesquisa espacial conector](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Estes objetos foram criados por outro motor de sincronização ou por um motor de sincronização com uma configuração de filtragem diferente. Estes objetos órfãos já não são geridos. Reveja esta lista e considere remover estes objetos utilizando os cmdlets De PowerShell da [AD Azure.](https://aka.ms/aadposh)

### <a name="cs-import"></a>Importação cs
Quando se abre um objeto CS, existem vários separadores no topo. O separador **Import** mostra os dados que são encenados após uma importação.  

![Screenshot da janela Propriedades do Objeto Espacial conector, com o separador Import selecionado](./media/tshoot-connect-object-not-syncing/csobject.png)    

A coluna **Old Value** mostra o que está atualmente armazenado no Connect, e a coluna **New Value** mostra o que foi recebido do sistema de origem e ainda não foi aplicado. Se houver um erro no objeto, as alterações não são processadas.

O separador **error de sincronização** só é visível na janela **Connector Space Object Properties** se houver algum problema com o objeto. Para mais informações, reveja como resolver erros de [sincronização no separador **Operações** ](#errors-on-the-operations-tab).

![Screenshot do separador erro de sincronização na janela Propriedades do Objeto Espacial do Conector](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Linhagem CS
O separador **Lineage** na janela Propriedades do **Objeto Espacial connector** mostra como o objeto de espaço do conector está relacionado com o objeto metaverso. Pode ver quando o conector importou pela última vez uma alteração do sistema conectado e quais as regras aplicadas para povoar dados no metaverso.  

![Screenshot mostrando o separador lineage na janela Propriedades do Objeto Espacial do Conector](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na figura anterior, a coluna **ação** apresenta uma regra de sincronização de entrada com a **disposição**de ação . Isto indica que enquanto este objeto espacial do conector estiver presente, o objeto metaverso permanece. Se a lista de regras de sincronização mostrar, em vez disso, uma regra de sincronização de saída com uma ação de **provisão,** este objeto é eliminado quando o objeto metaverso é eliminado.  

![Screenshot de uma janela de linhagem no separador lineage na janela Propriedades do Objeto Espacial conector](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na figura anterior, pode também ver na coluna **PasswordSync** que o espaço do conector de entrada pode contribuir com alterações na palavra-passe uma vez que uma regra de sincronização tem o valor **Verdadeiro**. Esta palavra-passe é enviada para a AD Azure através da regra de saída.

A partir do separador **Lineage,** pode chegar ao metaverso selecionando [**Propriedades metaversais**](#mv-attributes)de objetos .

### <a name="preview"></a>Pré-visualização
No canto inferior esquerdo da janela Propriedades do **Objeto Espacial do Conector** está o botão **Preview.** Selecione este botão para abrir a página **Preview,** onde pode sincronizar um único objeto. Esta página é útil se estiver a resolver algumas regras de sincronização personalizadas e quiser ver o efeito de uma mudança num único objeto. Pode selecionar uma **sincronização completa** ou uma **sincronização Delta.** Também pode selecionar **'Visualização geração',** que apenas mantém a mudança na memória. Ou selecione **'Pré-visualização',** que atualiza o metaverso e encena todas as alterações para os espaços do conector de destino.  

![Screenshot da página Pré-visualização, com Pré-visualização de início selecionado](./media/tshoot-connect-object-not-syncing/preview.png)  

Na pré-visualização pode inspecionar o objeto e ver qual a regra aplicada a um fluxo específico de atributos.  

![Screenshot da página de pré-visualização, mostrando fluxo de atributo de importação](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Registar
Ao lado do botão **'Pré-visualização',** selecione o botão **'Registar'** para abrir a página **'Registo'.** Aqui pode ver o estado de sincronização da palavra-passe e o histórico. Para mais informações, consulte a sincronização de hash de [palavra-passe de Troubleshoot com a sincronização Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriedades de objetos metaversos
Normalmente é melhor começar a procurar a partir do espaço de conector ative diretório. Mas também pode começar a procurar a partir do metaverso.

### <a name="searching-for-an-object-in-the-mv"></a>À procura de um objeto no MV
No Gestor de Serviços de Sincronização, selecione **Metaverse Search,** como na figura seguinte. Crie uma consulta que conhece encontra o utilizador. Procure por atributos comuns, tais como **nome de conta** **(sAMAccountName**) e **userPrincipalName**. Para mais informações, consulte a [pesquisa Metaverse do Gestor de Serviçosincronizado.](how-to-connect-sync-service-manager-ui-mvsearch.md)

![Screenshot do Gestor de Serviço de Sincronização, com o separador De Pesquisa Metaverse selecionado](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Na janela Resultados de **Pesquisa,** clique no objeto.

Se não encontrou o objeto, ainda não chegou ao metaverso. Continue a procurar o objeto no espaço do [conector](#connector-space-object-properties)de diretório ativo . Se encontrar o objeto no espaço do conector do Diretório Ativo, pode haver um erro de sincronização que está a impedir que o objeto venha para o metaverso, ou pode ser aplicada uma regra de sincronização do filtro de deteção.

### <a name="object-not-found-in-the-mv"></a>Objeto não encontrado no MV
Se o objeto estiver no Diretório Ativo CS mas não estiver presente no MV, é aplicado um filtro de deteção. Para ver o filtro de digitalização, vá ao menu de aplicações de desktop e selecione **Synchronization Rules Editor**. Filtre as regras aplicáveis ao objeto ajustando o filtro abaixo.

  ![Screenshot de Sincronização Regras Editor, mostrando uma pesquisa de regras de sincronização de entrada](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Ver cada regra na lista de cima e verificar o **filtro Scoping**. No filtro de deteção seguinte, se o valor **isCriticalSystemObject** for nulo ou FALSO ou vazio, está no âmbito.

  ![Screenshot de um filtro de deteção em uma pesquisa de regra de sincronização de entrada](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Vá à lista de atributos [de Importação CS](#cs-import) e verifique qual o filtro que está a bloquear a mudança do objeto para o MV. A lista de atributos do **Espaço conector** mostrará apenas atributos não nulos e não vazios. Por exemplo, se **for o CriticalSystemObject** não aparecer na lista, o valor deste atributo é nulo ou vazio.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objeto não encontrado no CS AD Azure
Se o objeto não estiver presente no espaço do conector da AD Azure, mas estiver presente no MV, veja o filtro de deteção das regras de saída do espaço conector correspondente e descubra se o objeto é filtrado porque os [atributos MV](#mv-attributes) não satisfazem os critérios.

Para ver o filtro de deteção de saída, selecione as regras aplicáveis para o objeto ajustando o filtro abaixo. Ver cada regra e olhar para o valor correspondente do [atributo MV.](#mv-attributes)

  ![Screenshot de uma pesquisa de regras de sincronização de saída no Editor de Regras de Sincronização](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atributos MV
No separador **Atributos,** pode ver os valores e quais os conectores que os contribuíram.  

![Screenshot da janela Metaverse Object Properties, com o separador Atributos selecionado](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Se um objeto não estiver a sincronizar, faça as seguintes perguntas sobre os estados atributos no metaverso:
- A nuvem de atributoé **filtrada** e definida para **True?** Se for, foi filtrado de acordo com os passos na [filtragem à base de atributos](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- O atributo **fonteAnchor** está presente? Se não, tem uma topologia florestal de recursos de conta? Se um objeto for identificado como uma caixa de correio ligada (o atributo **msExchRecipientTypeDetails** tem o valor **2**), a **fonteAnchor** é contribuído pela floresta com uma conta ative Directy ativada. Certifique-se de que a conta principal foi importada e sincronizada corretamente. A conta principal deve ser listada entre os [conectores](#mv-connectors) do objeto.

### <a name="mv-connectors"></a>Conectores MV
O separador **Conectores** mostra todos os espaços de conectores que têm uma representação do objeto. 
 
![Screenshot da janela Metaverse Object Properties, com o separador Conectores selecionado](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Deve ter um conector para:

- Cada floresta de Diretório Ativo em que o utilizador está representado. Esta representação pode incluir **diretores de segurança estrangeiros** e objetos de **contacto.**
- Um conector em Azure AD.

Se faltar o conector ao Azure AD, reveja a secção de [atributos MV](#mv-attributes) para verificar os critérios de fornecimento ao Azure AD.

A partir do separador **Conectores** também pode ir ao [objeto espacial do conector](#connector-space-object-properties). Selecione uma linha e clique em **Propriedades**.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre a [sincronização Azure AD Connect](how-to-connect-sync-whatis.md).
- Saiba mais sobre [identidade híbrida.](whatis-hybrid-identity.md)
