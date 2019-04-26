---
title: Resolver problemas de um objeto que não estiver a sincronizar com o Azure Active Directory | Documentos da Microsoft
description: Resolver problemas de um objeto que não estiver a sincronizar com o Azure Active Directory.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455235"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Resolver problemas de um objeto que não está a sincronizar com o Azure Active Directory

Se um objeto não estiver a sincronizar conforme esperado com o Microsoft Azure Active Directory (Azure AD), pode ser devido a vários motivos. Se receber um e-mail de erro do Azure AD ou vir o erro no Azure AD Connect Health, leia [resolver problemas de erros durante a sincronização](tshoot-connect-sync-errors.md) em vez disso. Mas se estiver a resolver um problema em que o objeto não está no Azure AD, este artigo é para. Ele descreve como encontrar erros no local de sincronização do componente do Azure AD Connect.

>[!IMPORTANT]
>Para o Azure AD Connect implementação com a versão 1.1.749.0 ou superior, utilize o [tarefas de resolução de problemas](tshoot-connect-objectsync.md) no Assistente para resolver problemas de sincronização de objeto. 

## <a name="synchronization-process"></a>Processo de sincronização

Antes, vamos investigar problemas de sincronização, vamos compreender o Azure AD Connect a sincronizar o processo:

  ![Diagrama do processo de sincronização do Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço conector, uma tabela numa base de dados
* **MV:** Metaverso, uma tabela numa base de dados

### <a name="synchronization-steps"></a>**Passos de sincronização**
O processo de sincronização envolve os seguintes passos:

1. **Importar do AD:** Objetos do Active Directory são colocados em CS de diretório Active Directory.

2. **Importação do Azure AD:** Objetos do AD Azure trazidos para o Azure AD CS.

3. **Sincronização:** Regras de sincronização de entrada e regras de sincronização de saída são executadas na ordem de número de precedência, de inferior para superior. Para ver as regras de sincronização, vá para o Editor de regras de sincronização das aplicações de ambiente de trabalho. As regras de sincronização de entrada inclua dados de CS para MV. As regras de sincronização de saída mover dados de MV ao CS.

4. **Exportar para o AD:** Depois de sincronizar, objetos são exportados a partir do CS de diretório Active Directory para o Active Directory.

5. **Exportar para o Azure AD:** Depois de sincronizar, objetos são exportados a partir do Azure AD CS para o Azure AD.

## <a name="troubleshooting"></a>Resolução de problemas

Para localizar os erros, observe alguns lugares, pela seguinte ordem:

1. O [registos de operações](#operations) para localizar os erros identificados pelo motor de sincronização durante a importação e sincronização.
2. O [espaço conector](#connector-space-object-properties) para localizar objetos em falta e erros de sincronização.
3. O [metaverso](#metaverse-object-properties) para localizar problemas relacionados com dados.

Inicie [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) antes de iniciar estes passos.

## <a name="operations"></a>Operações
O **operações** guia no Synchronization Service Manager é onde deve começar a sua solução de problemas. Este separador mostra os resultados das operações mais recentes. 

![Captura de ecrã do Synchronization Service Manager, que mostra a guia de operações selecionado](./media/tshoot-connect-object-not-syncing/operations.png)  

Na metade superior a **operações** separador mostra todas as execuções por ordem cronológica. Por predefinição, as operações de registo mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [agendador](how-to-connect-sync-feature-scheduler.md). Vista de olhos para que execute qualquer um que não mostra um **êxito** estado. Pode alterar a ordenação ao clicar nos cabeçalhos.

O **estado** coluna contém as informações mais importantes e mostra o problema mais grave para uma execução. Aqui está um resumo rápido dos Estados mais comuns na ordem de prioridade de investigação (onde * indica várias cadeias de caracteres de erro possíveis).

| Estado | Comentário |
| --- | --- |
| stopped-* |Não foi possível concluir a execução. Isto pode acontecer, por exemplo, se o sistema remoto está inativo e não pode ser contactado. |
| stopped-error-limit |Há mais de 5.000 erros. A execução automaticamente foi parada devido ao grande número de erros. |
| concluída -\*-erros |A execução foi concluída, mas existem erros (menos de 5.000), que devem ser investigados. |
| concluída -\*-avisos |A execução foi concluída, mas alguns dados não estão no estado esperado. Se tiver erros, esta mensagem é, normalmente, apenas um sintoma. Não investigar avisos até que a corrigir esses erros. |
| exito |Não existem problemas. |

Quando seleciona uma linha, a parte inferior dos **operações** separador é atualizado para mostrar os detalhes dessa execução. No lado extrema esquerda nessa área, poderá ter uma lista, intitulada **passo n. º**. Esta lista é apresentada apenas se tiver vários domínios na sua floresta e cada domínio é representado por um passo. O nome de domínio pode ser encontrado no cabeçalho **partição**. Sob o **estatísticas de sincronização** cabeçalho, pode encontrar mais informações sobre o número de alterações que foram processados. Selecione as ligações para obter uma lista dos objetos alterados. Se tiver objetos com erros, esses erros apresentados no **erros de sincronização** cabeçalho.

### <a name="errors-on-the-operations-tab"></a>Erros na guia operações
Quando tiver erros, o Synchronization Service Manager mostra o objeto erro e o próprio erro como ligações fornecem mais informações.

![Captura de ecrã de erros no Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Comece por selecionar a cadeia de erro. (Na figura anterior, a cadeia de erro é **sincronização regra-erro-função-acionada**.) É apresentada pela primeira vez com uma visão geral do objeto. Para ver o erro real, selecione **rastreio de pilha**. Este rastreio fornece informações de nível de depuração para o erro.

Com o botão direito a **chamar informações de pilha** , clique em **Selecionar tudo**e, em seguida, selecione **cópia**. Em seguida, copie a pilha e examinar o erro no seu editor favorito, como o bloco de notas.

Se o erro é partir **SyncRulesEngine**, as informações da pilha de chamada primeiro apresenta uma lista de todos os atributos no objeto. Desloque para baixo até ver o rumo **InnerException = >**.  

  ![Captura de ecrã do Synchronization Service Manager, que mostra informações de erro sob o cabeçalho InnerException = >](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
A linha após o cabeçalho mostra o erro. Na figura anterior, o erro é de uma regra de sincronização personalizado que criou da Fabrikam.

Se o erro fornece informações suficientes, é hora de examinar os dados em si. Selecione a ligação com o identificador de objeto e continuar a resolução de problemas a [objeto importado do espaço conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propriedades de objeto de espaço conector
Se o [ **operações** ](#operations) separador mostra sem erros, siga o objeto de espaço conector do Active Directory para o metaverso para o Azure AD. Esse caminho, deve encontrar onde está o problema.

### <a name="searching-for-an-object-in-the-cs"></a>A procurar um objeto no CS

No Gestor do serviço de sincronização, selecione **conectores**, selecione o conector do Active Directory e selecione **procurar espaço conector**.

Na **âmbito** caixa, selecione **RDN** quando pretender uma procura o atributo CN, ou selecione **DN ou âncora** quando pretender procurar no **distinguishedName**  atributo. Introduza um valor e selecione **pesquisa**. 
 
![Captura de ecrã de uma pesquisa de espaço conector](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Se não encontrar o objeto que procura, pode ter sido filtrado com [filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ou [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Para verificar se a filtragem é configurada como esperado, leia [do Azure AD Connect: Configurar a filtragem](how-to-connect-sync-configure-filtering.md).

Pode executar outra pesquisa útil ao selecionar o conector do Azure AD. Na **âmbito** caixa, selecione **importação**e, em seguida, selecione o **Add** caixa de verificação. Esta pesquisa dá-lhe todos os objetos sincronizados no Azure AD que não pode ser associado um objeto no local.  

![Captura de ecrã do órfãos numa pesquisa de espaço conector](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Esses objetos foram criados por outro motor de sincronização ou de um motor de sincronização com uma configuração de filtragem diferente. Esses objetos órfãos já não são geridos. Reveja esta lista e considere remover estes objetos, utilizando o [do Azure AD PowerShell](https://aka.ms/aadposh) cmdlets.

### <a name="cs-import"></a>Importação de CS
Quando abrir um objeto de CS, existem várias guias na parte superior. O **importar** separador mostra os dados preparados após uma importação.  

![Captura de ecrã da janela de propriedades de objeto de espaço conector, o separador de importação selecionado](./media/tshoot-connect-object-not-syncing/csobject.png)    

O **valor antigo** coluna mostra o que atualmente é armazenado no Connect e o **novo valor** coluna mostra o que foi recebido do sistema de origem e ainda não foi aplicado. Se existir um erro no objeto, as alterações não são processadas.

O **erro de sincronização** separador está visível no **propriedades de objeto de espaço conector** apenas se existe um problema com o objeto de janela. Para obter mais informações, consulte como [resolver problemas de erros de sincronização no **operações** separador](#errors-on-the-operations-tab).

![Captura de ecrã do separador de erro de sincronização na janela de propriedades de objeto de espaço conector](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Linhagem CS
O **linhagem** separador a **propriedades de objeto de espaço conector** janela mostra como o objeto de espaço conector está relacionado ao objeto de metaverso. Pode ver quando o conector importado pela última vez uma alteração do sistema ligado e quais regras aplicadas para preencher dados no metaverso.  

![Captura de ecrã que mostra o separador de linhagem na janela de propriedades de objeto de espaço conector](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na figura anterior, o **ação** coluna mostra uma regra de sincronização de entrada com a ação **aprovisionar**. Que indica que, desde que este objeto de espaço conector estiver presente, o objeto de metaverso permanece. Se a lista de regras de sincronização mostra em vez disso, uma regra de sincronização de saída com um **aprovisionar** ação, este objeto é eliminada quando o objeto de metaverso é eliminado.  

![Captura de ecrã de uma janela de linhagem no separador de linhagem na janela de propriedades de objeto de espaço conector](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na figura anterior, também pode ver na **PasswordSync** coluna que o espaço conector de entrada pode contribuir com alterações para a palavra-passe, uma vez que uma regra de sincronização tem o valor **verdadeiro**. Esta palavra-passe é enviada para o Azure AD através da regra de saída.

Do **linhagem** separador, pode obter para o metaverso selecionando [ **propriedades do objeto de Metaverso**](#mv-attributes).

### <a name="preview"></a>Pré-visualização
No canto inferior esquerdo dos **propriedades de objeto de espaço conector** janela é a **pré-visualização** botão. Selecione este botão para abrir o **pré-visualização** página, onde pode sincronizar um único objeto. Esta página é útil se estiver a resolver algumas regras de sincronização personalizado e quiser ver o efeito de uma alteração num único objeto. Pode selecionar uma **Full sync** ou uma **sincronização Delta**. Também pode selecionar **gerar pré-visualização**, apenas que mantém a alteração na memória. Ou selecione **consolidar pré-visualização**, que atualiza o metaverso e prepara a todas as alterações aos espaços de conector de destino.  

![Captura de ecrã da página de pré-visualização, com iniciar pré-visualização selecionado](./media/tshoot-connect-object-not-syncing/preview.png)  

Na pré-visualização, pode inspecionar o objeto e ver quais regras aplicadas para um fluxo de atributos específico.  

![Captura de ecrã da página de pré-visualização, que mostra o fluxo de atributos de importação](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Registar
Junto aos **pré-visualização** botão, selecione a **Log** botão para abrir o **Log** página. Aqui pode ver o estado da sincronização de palavra-passe e o histórico. Para obter mais informações, consulte [resolver problemas de sincronização de hash de palavra-passe com o Azure AD Connect sync](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriedades do objeto de Metaverso
Em geral é melhor iniciar a procura da origem de espaço conector do Active Directory. Mas também pode iniciar a pesquisa do metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>A procurar um objeto de MV
No Gestor do serviço de sincronização, selecione **pesquisa de Metaverso**, como na figura a seguir. Crie uma consulta que sabe que encontra o usuário. Procurar atributos comuns, como **accountName** (**sAMAccountName**) e **userPrincipalName**. Para obter mais informações, consulte [pesquisa de Metaverso do Gestor do serviço de sincronização](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Captura de ecrã do Synchronization Service Manager, o separador de pesquisa de Metaverso selecionado](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Na **resultados da pesquisa** janela, clique com o objeto.

Se não encontrou o objeto, ele ainda não atingiu o metaverse. Continuar para procurar o objeto no Active Directory [espaço conector](#connector-space-object-properties). Se encontrar o objeto no espaço conector do Active Directory, pode haver um erro de sincronização que está a bloquear o objeto do metaverso a chegar, ou pode ser aplicado um filtro de âmbito de regra de sincronização.

### <a name="object-not-found-in-the-mv"></a>Objekt nebyl nalezen v a MV
Se o objeto está no CS de diretório Active Directory, mas não estão presentes na MV, é aplicado um filtro de âmbito. Para ver o filtro de âmbito, vá para o menu de aplicativo de desktop e selecione **Editor de regras de sincronização**. Filtre regras aplicáveis para o objeto ao ajustar o filtro abaixo.

  ![Captura de ecrã de regras de Editor de sincronização, que mostra uma pesquisa de regras de sincronização de entrada](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Ver cada regra na lista acima e verifique o **Scoping filtro**. No seguinte filtro de âmbito, se o **isCriticalSystemObject** valor é nulo ou FALSO ou está vazio, esteja no âmbito.

  ![Captura de ecrã de um filtro de âmbito numa pesquisa de regra de sincronização de entrada](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Vá para o [importação CS](#cs-import) lista e verifique qual filtro está a bloquear o objeto da passagem para a MV de atributo. O **espaço conector** da lista de atributos mostrará apenas os atributos de não-null e não vazio. Por exemplo, se **isCriticalSystemObject** não aparecer na lista, o valor deste atributo é nulo ou estar vazio.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objeto não foi encontrado no Azure AD CS
Se o objeto não está presente no espaço conector do Azure AD, mas não existe a MV, veja o filtro de âmbito das regras de saída do espaço conector correspondente e descubra se o objeto é filtrado porque o [atributos de MV](#mv-attributes)não cumprem os critérios.

Para ver o filtro de âmbito de saída, selecione as regras aplicável para o objeto ao ajustar o filtro abaixo. Ver cada regra e examinar o correspondente [atributo de MV](#mv-attributes) valor.

  ![Captura de ecrã de uma pesquisa de regras de sincronização de saída no Editor de regras de sincronização](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atributos de MV
Sobre o **atributos** guia, pode ver os valores e os conectores contribuiu com eles.  

![Captura de ecrã da janela Propriedades do objeto de Metaverso, o separador de atributos selecionado](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Se não estiver a sincronizar um objeto, faça as seguintes perguntas sobre Estados de atributo no metaverso:
- É o atributo **cloudFiltered** presente e definido como **verdadeiro**? Se for, ele foi filtrado, de acordo com os passos em [filtragem baseada em atributo](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- É o atributo **sourceAnchor** presente? Caso contrário, tem uma topologia de floresta de recursos da conta? Se um objeto é identificado como uma caixa de correio vinculada (o atributo **msExchRecipientTypeDetails** tem o valor **2**), o **sourceAnchor** contribuiu de floresta com um Ativar a conta do Active Directory. Certifique-se de que a conta mestre foi importada e sincronizada corretamente. A conta principal deve ser listada entre os [conectores](#mv-connectors) para o objeto.

### <a name="mv-connectors"></a>Conectores de MV
O **conectores** separador mostra todos os espaços de conectores que tenham uma representação do objeto. 
 
![Captura de ecrã da janela Propriedades do objeto de Metaverso, com o separador conectores selecionado](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Deve ter um conector para:

- Cada floresta do Active Directory do usuário é representada no. Pode incluir essa representação **foreignSecurityPrincipals** e **contacto** objetos.
- Um conector no Azure AD.

Se estiver em falta o conector para o Azure AD, leia a seção sobre [atributos de MV](#mv-attributes) para verificar os critérios para o aprovisionamento para o Azure AD.

Do **conectores** separador, também pode ir para o [objeto do espaço conector](#connector-space-object-properties). Selecione uma linha e clique em **propriedades**.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [do Azure AD Connect](how-to-connect-sync-whatis.md).
- Saiba mais sobre [identidade híbrida](whatis-hybrid-identity.md).
