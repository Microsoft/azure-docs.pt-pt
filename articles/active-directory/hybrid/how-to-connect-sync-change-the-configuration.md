---
title: 'Azure AD Connect Sync: Faça uma alteração na configuração padrão'
description: Acompanha-o como fazer uma alteração na configuração na sincronização Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2044653673da10de59d5ff125da44ac1f89e22f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861855"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect Sync: Faça uma alteração na configuração padrão
O objetivo deste artigo é interprir como fazer alterações na configuração padrão no Azure Ative Directory (Azure AD) Conecte a sincronização. Fornece passos para alguns cenários comuns. Com este conhecimento, deverá ser capaz de fazer alterações simples na sua própria configuração com base nas suas próprias regras de negócio.

> [!WARNING]
> Se efec se fizer alterações às regras de sincronização fora da caixa, estas alterações serão substituídas da próxima vez que o Azure AD Connect for atualizado, resultando em resultados inesperados e prováveis de sincronização indesejados.
>
> As regras de sincronização fora da caixa têm uma impressão digital. Se fizer uma alteração a estas regras, a impressão digital já não corresponde. Poderá ter problemas no futuro quando tentar aplicar um novo lançamento do Azure AD Connect. Só faça alterações da forma como é descrito neste artigo.

## <a name="synchronization-rules-editor"></a>Editor de Regras de Sincronização
O Editor de Regras de Sincronização é utilizado para ver e alterar a configuração padrão. Pode encontrá-lo no menu **Iniciar** sob o grupo **Azure AD Connect.**  
![Menu iniciar com Sync Rule Editor](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Quando abres o editor, vês as regras fora da caixa.

![Sync Rule Editor](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegando no editor
Usando as gotas no topo do editor, pode rapidamente encontrar uma regra específica. Por exemplo, se quiser ver as regras em que o atributo ProxyAddresses está incluído, pode alterar as descidas para as seguintes:  
![Filtragem SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Para reiniciar a filtragem e carregar uma configuração nova, prima F5 no teclado.

No canto superior direito encontra-se o botão **adicionar nova regra.** Use este botão para criar a sua própria regra personalizada.

Na parte inferior encontram-se botões para agir numa regra de sincronização selecionada. **Editar** e **Eliminar** faça o que espera. **A exportação** produz um script PowerShell para recriar a regra de sincronização. Com este procedimento, pode mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Crie a sua primeira regra personalizada
As alterações mais comuns são para os fluxos de atributos. Os dados no seu diretório de origem podem não ser os mesmos que no Azure AD. No exemplo desta secção, certifique-se de que o nome próprio de um utilizador está sempre no *caso adequado*.

### <a name="disable-the-scheduler"></a>Desative o programador
O [programador](how-to-connect-sync-feature-scheduler.md) funciona a cada 30 minutos por defeito. Certifique-se de que não está a começar enquanto está a fazer alterações e a resolver problemas com as suas novas regras. Para desativar temporariamente o programador, inicie o PowerShell e corra `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Desative o programador](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra
1. Clique **Em Adicionar nova regra.**
2. Na página **Descrição,** insira o seguinte:  
   ![Filtragem da regra de entrada](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Nome**: Dê à regra um nome descritivo.
   * **Descrição**: Dê alguns esclarecimentos para que alguém possa entender para que é a regra.
   * **Sistema ligado**: Este é o sistema em que o objeto pode ser encontrado. Neste caso, selecione **Ative Directory Connector**.
   * **Tipo de objeto ligado sistema/metaverso**: Selecione **Utilizador** e **Pessoa,** respectivamente.
   * **Tipo de Ligação**: Altere este valor para **Juntar.**
   * **Precedência**: Forneça um valor único no sistema. Um valor numérico inferior indica uma precedência mais elevada.
   * **Tag**: Deixe isto vazio. Apenas as regras fora da caixa da Microsoft devem ter esta caixa preenchida com um valor.
3. Na página do **filtro de escoar,** **insira o nome ISNOTNULL**.  
   ![Filtro de deteção de regras de entrada](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Esta secção é utilizada para definir quais os objetos a que a regra deve aplicar-se. Se ficar vazio, a regra aplicar-se-ia a todos os objetos do utilizador. No entanto, isso incluiria salas de conferências, contas de serviço e outros objetos de utilizador não-pessoas.
4. Na página **'Regras 'Unir',** deixe o campo vazio.
5. Na página **Transformações,** **altere o FlowType** para **a Expressão**. Para **o Atributo Alvo**, selecione dado **Nome**. E para **a Fonte**, insira **pCase ([dado Nome])**.
   ![Transformações de regras de entrada](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   O motor de sincronização é sensível a maiíssimos para o nome da função e para o nome do atributo. Se escrever algo errado, vê um aviso quando adiciona a regra. Podes poupar e continuar, mas tens de reabrir e corrigir a regra.
6. Clique **em Adicionar** para guardar a regra.

A sua nova regra personalizada deve ser visível com as outras regras de sincronização do sistema.

### <a name="verify-the-change"></a>Verifique a alteração
Com esta nova mudança, você quer ter certeza de que está funcionando como esperado e não está jogando nenhum erro. Dependendo do número de objetos que tem, há duas maneiras de fazer este passo:

- Executar uma sincronização completa em todos os objetos.
- Executar uma pré-visualização e sincronização completa num único objeto.

Abra o **Serviço de Sincronização** a partir do menu **Iniciar.** Os passos desta secção estão todos nesta ferramenta.

**Sincronização completa em todos os objetos**  

   1. Selecione **Conectores** na parte superior. Identifique o conector que alterou na secção anterior (neste caso, Serviços de Domínio do Diretório Ativo) e selecione-o. 
   2. Para **Ações**, selecione **Executar**.
   3. Selecione **a sincronização completa** e, em seguida, selecione **OK**.
   ![Sincronização completa](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Os objetos são agora atualizados no metaverso. Verifique as alterações olhando para o objeto no metaverso.

**Pré-visualização e sincronização completa num único objeto**  

   1. Selecione **Conectores** na parte superior. Identifique o conector que alterou na secção anterior (neste caso, Serviços de Domínio do Diretório Ativo) e selecione-o.
   2. Selecione **espaço de conector de pesquisa**. 
   3. Use **scope** para encontrar um objeto que pretende utilizar para testar a alteração. Selecione o objeto e clique em **Pré-visualização**. 
   4. No novo ecrã, **selecione Commit Preview**.  
   ![Comprometer pré-visualização](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   A mudança está agora comprometida com o metaverso.

**Ver o objeto no metaverso**  

1. Escolha alguns objetos de amostra para se certificar de que o valor é esperado e que a regra é aplicada. 
2. Selecione **Metaverse Search** a partir do topo. Adicione qualquer filtro que necessite para encontrar os objetos relevantes. 
3. A partir do resultado da pesquisa, abra um objeto. Veja os valores do atributo e verifique também na coluna **Sync Rules** que a regra se aplicava como esperado.  
![Pesquisa de metaversos](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Ativar o programador
Se tudo estiver como esperado, pode voltar a ativar o programador. Da PowerShell, `Set-ADSyncScheduler -SyncCycleEnabled $true` corra.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações comuns do fluxo de atributos
A secção anterior descreveu como fazer alterações num fluxo de atributos. Nesta secção, são fornecidos alguns exemplos adicionais. Os passos para criar a regra de sincronização são abreviados, mas pode encontrar todos os passos na secção anterior.

### <a name="use-an-attribute-other-than-the-default"></a>Use um atributo diferente do padrão
Neste cenário de Fabrikam, há uma floresta onde o alfabeto local é usado para nome, apelido e nome de exibição. A representação latina destes atributos pode ser encontrada nos atributos de extensão. Para a construção de uma lista global de endereços em AZure AD e Microsoft 365, a organização quer usar estes atributos em vez disso.

Com uma configuração padrão, um objeto da floresta local é assim:  
![Fluxo de atributo 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, faça o seguinte:

1. Abra o **Editor de Regras** de Sincronização a partir do menu **Iniciar.**
2. Com **a Entrada** ainda selecionada à esquerda, clique no **botão Adicionar nova regra.**
3. Dê à regra um nome e descrição. Selecione a instância do Ative Directory no local e os tipos de objetos relevantes. No **Tipo de Ligação,** selecione **'Juntar-se'.** Para **Precedência,** escolha um número que não seja utilizado por outra regra. As regras fora da caixa começam com 100, para que o valor 50 possa ser usado neste exemplo.
  ![Fluxo de atributo 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Deixe **o filtro de escotagem** vazio. (Isto é, deve aplicar-se a todos os objetos de utilizador na floresta.)
5. Deixe **as regras de join vazias.** (Isto é, deixe a regra fora da caixa lidar com qualquer junção.)
6. Em **Transformações,** crie os seguintes fluxos:  
  ![Fluxo de atributo 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Clique **em Adicionar** para guardar a regra.
8. Vá ao Gestor de **Serviços de Sincronização.** Nos **Conectores,** selecione o conector onde adicionou a regra. Selecione **Executar** e, em seguida, selecione **a sincronização completa**. Uma sincronização completa recalcula todos os objetos utilizando as regras atuais.

Este é o resultado para o mesmo objeto com esta regra personalizada:  
![Fluxo de atributo 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento dos atributos
Os atributos de corda são indexáveis por padrão, e o comprimento máximo é de 448 caracteres. Se estiver a trabalhar com atributos de corda que possam conter mais, certifique-se de incluir o seguinte no fluxo de atributos:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Alterar o utilizadorPrincipalS sulé
O atributo UserPrincipalName no Ative Directory nem sempre é conhecido pelos utilizadores e pode não ser adequado como o ID de inscrição. Com o assistente de instalação de sincronização Azure AD Connect, pode escolher um atributo diferente -- por exemplo, *correio*. Mas em alguns casos, o atributo deve ser calculado.

Por exemplo, a empresa Contoso tem dois diretórios AD Azure, um para produção e outro para testes. Querem que os utilizadores do seu inquilino de teste utilizem outro sufixo na iD de inscrição:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Nesta expressão, pegue em tudo o que resta do primeiro @-sign (Word) e concatenate com uma corda fixa.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Converter um atributo multi-valor para um único valor
Alguns atributos no Ative Directory são multi-valorizados no esquema, mesmo que pareçam valor único em Utilizadores e Computadores de Diretório Ativo. Um exemplo é o atributo descrição:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Nesta expressão, se o atributo tiver um valor, pegue o primeiro item (*Item*) no atributo, remova os espaços de liderança e de fuga *(Trim),* e, em seguida, mantenha os primeiros 448 caracteres *(Esquerda)* na corda.

### <a name="do-not-flow-an-attribute"></a>Não fluir um atributo
Para obter antecedentes sobre o cenário para esta secção, consulte [Controle o processo de fluxo de atributos](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Há duas maneiras de não fluir um atributo. A primeira é utilizar o assistente de instalação para [remover atributos selecionados](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Esta opção funciona se nunca tiver sincronizado o atributo antes. No entanto, se começou a sincronizar este atributo e posteriormente removê-lo com esta função, o motor de sincronização deixa de gerir o atributo e os valores existentes ficam em Azure AD.

Se quiser remover o valor de um atributo e certificar-se de que não flui no futuro, precisa de criar uma regra personalizada.

Neste cenário de Fabrikam, percebemos que alguns dos atributos que sincronizamos com a nuvem não deveriam estar lá. Queremos certificar-nos de que estes atributos são removidos do Azure AD.  
![Atributos de extensão ruim](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Crie uma nova regra de sincronização de entrada e povoe a descrição.
  ![Descrições](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Criar fluxos de atributos com **Expression** for **FlowType** e com **AuthoritituativeNull** para **Origem**. O **literativo** literal indica que o valor deve estar vazio no metaverso, mesmo que uma regra de sincronização de precedência inferior tente preencher o valor.
  ![Transformação para atributos de extensão](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Guarde a regra da sincronização. Iniciar o **Serviço de Sincronização**, encontrar o conector, selecionar **Executar** e, em seguida, selecionar **a Sincronização Completa**. Este passo recalcula todos os fluxos de atributos.
4. Verifique se as alterações previstas estão prestes a ser exportadas através da pesquisa no Espaço do Conector.
  ![Eliminação encenada](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Criar regras com PowerShell
A utilização do editor de regras de sincronização funciona bem quando só tens algumas alterações a fazer. Se precisar de fazer muitas alterações, o PowerShell pode ser uma opção melhor. Algumas funcionalidades avançadas só estão disponíveis com o PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obtenha o script PowerShell para uma regra fora da caixa
Para ver o script PowerShell que criou uma regra fora da caixa, selecione a regra no editor de regras de sincronização e clique em **Exportação**. Esta ação dá-lhe o script PowerShell que criou a regra.

### <a name="advanced-precedence"></a>Precedência avançada
As regras de sincronização fora da caixa começam com um valor de precedência de 100. Se você tem muitas florestas e precisa fazer muitas mudanças personalizadas, então 99 regras de sincronização podem não ser suficientes.

Pode instruir o motor de sincronização de que pretende que as regras adicionais são inseridas antes das regras fora de caixa. Para obter este comportamento, siga estes passos:

1. Marque a primeira regra de sincronização fora da caixa (**In from AD-User Join**) no editor de regras de sincronização e selecione **Export**. Copie o valor do identificador SR.  
![PowerShell antes da mudança](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Crie a nova regra de sincronização. Pode usar o editor de regras de sincronização para criá-lo. Exporte a regra para um script PowerShell.
3. Na propriedade **Precedence Antes,** insira o valor do Identificador a partir da regra fora da caixa. Definir a **Precedência** para **0**. Certifique-se de que o atributo Identifier é único e que não está a reutilizar um GUID de outra regra. Certifique-se também de que a propriedade **ImuttableTag** não está definida. Esta propriedade deve ser definida apenas para uma regra fora da caixa.
4. Guarde o script PowerShell e execute-o. O resultado é que a sua regra personalizada é atribuída o valor de precedência de 100 e todas as outras regras fora da caixa são incrementadas.  
![PowerShell após a mudança](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Pode ter muitas regras de sincronização personalizadas usando o mesmo **valor precedência Antes do** valor necessário.

## <a name="enable-synchronization-of-usertype"></a>Permitir a sincronização do UserType
O Azure AD Connect suporta a sincronização do atributo **UserType** para objetos **do Utilizador** na versão 1.1.524.0 e posterior. Mais concretamente, foram introduzidas as seguintes alterações:

- O esquema do tipo de objeto **Utilizador** no Conector AZURE AD é estendido para incluir o atributo UserType, que é da cadeia tipo e é de valor único.
- O esquema do tipo de objeto **Pessoa** no metaverso é estendido para incluir o atributo UserType, que é do tipo de cadeia e é de valor único.

Por predefinição, o atributo UserType não está ativado para sincronização porque não existe um atributo UserType correspondente no Ative Directory. Deve ativar manualmente a sincronização. Antes de o fazer, deve tomar nota do seguinte comportamento imposto pela Azure AD:

- A Azure AD só aceita dois valores para o atributo UserType: **Membro** e **Convidado**.
- Se o atributo UserType não estiver habilitado para sincronização no Azure AD Connect, os utilizadores de AD Azure criados através da sincronização de diretórios teriam o atributo UserType definido para **o Membro**.
- Antes da versão 1.5.30.0, a Azure AD não permitiu que o atributo UserType nos utilizadores AD Azure existentes fosse alterado pelo Azure AD Connect. Nas versões mais antigas, só poderia ser definido durante a criação dos utilizadores AD Azure e [alterado via PowerShell](/powershell/module/azuread/set-azureaduser).

Antes de permitir a sincronização do atributo UserType, deve primeiro decidir como o atributo é derivado do Ative Directory no local. Seguem-se as abordagens mais comuns:

- Designar um atributo AD não utilizado no local (como extensãoAttribute1) para ser usado como atributo de origem. O atributo AD no local designado deve ser do tipo **de cadeia,** ser de valor único, e conter o **valor Membro** ou **Convidado**. 

    Se escolher esta abordagem, deve certificar-se de que o atributo designado é preenchido com o valor correto para todos os objetos de utilizador existentes no Ative Directory que são sincronizados com AD azul antes de permitir a sincronização do atributo UserType.

- Em alternativa, pode obter o valor do atributo UserType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como **Guest** se o seu atributo De nome de utilizador AD no local terminar com a parte de domínio <em>@partners.fabrikam123.org</em> . 

    Como mencionado anteriormente, as versões mais antigas do Azure AD Connect não permitem que o atributo UserType nos utilizadores AZure AD existentes seja alterado pelo Azure AD Connect. Por isso, deve certificar-se de que a lógica que decidiu é consistente com a forma como o atributo UserType já está configurado para todos os utilizadores AZure AD existentes no seu inquilino.

Os passos para permitir a sincronização do atributo UserType podem ser resumidos como:

1.  Desative o programador de sincronização e verifique se não existe sincronização em curso.
2.  Adicione o atributo de origem ao esquema do conector AD no local.
3.  Adicione o UserType ao esquema do conector AD Azure.
4.  Crie uma regra de sincronização de entrada para fluir o valor do atributo a partir do Ative Directory no local.
5.  Crie uma regra de sincronização de saída para fluir o valor do atributo para Azure AD.
6.  Executar um ciclo completo de sincronização.
7.  Ativar o programador de sincronização.

>[!NOTE]
> O resto desta secção cobre estes passos. São descritos no contexto de uma implantação AZure AD com topologia de floresta única e sem regras de sincronização personalizadas. Se tiver topologia multi-floresta, regras de sincronização personalizadas configuradas ou ter um servidor de encenação, tem de ajustar os passos em conformidade.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desative o programador de sincronização e verifique se não há sincronização em curso
Para evitar exportar alterações não intencionais para a Azure AD, certifique-se de que não ocorre sincronização enquanto estiver a atualizar as regras de sincronização. Para desativar o programador de sincronização incorporado:

 1. Inicie uma sessão PowerShell no servidor Azure AD Connect.
 2. Desative a sincronização programada executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 3. Abra o Gestor de Serviços de Sincronização indo para **iniciar o** Serviço  >  **de Sincronização**.
 4. Vá ao separador **Operações** e confirme que não existe qualquer operação com um estado de *progresso.*

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Passo 2: Adicione o atributo de origem ao esquema de conector AD no local
Nem todos os atributos AD AZure são importados para o espaço de conector AD no local. Para adicionar o atributo de origem à lista dos atributos importados:

 1. Aceda ao **separador Conectores** no Gestor de Serviço de Sincronização.
 2. Clique com o botão direito no conector AD no local e selecione **Propriedades.**
 3. Na caixa de diálogo pop-up, aceda ao **separador 'Selecionar Atributos'.**
 4. Certifique-se de que o atributo de origem é verificado na lista de atributos.
 5. Clique **em OK** para guardar.
![Adicionar atributo de origem ao esquema de conector AD no local](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>Passo 3: Adicione o atributo UserType ao esquema do conector AD Azure
Por predefinição, o atributo UserType não é importado para o Azure AD Connect Space. Para adicionar o atributo UserType à lista de atributos importados:

 1. Aceda ao **separador Conectores** no Gestor de Serviço de Sincronização.
 2. Clique com o botão direito no **Conector AD Azure** e selecione **Propriedades**.
 3. Na caixa de diálogo pop-up, aceda ao **separador 'Selecionar Atributos'.**
 4. Certifique-se de que o atributo UserType está verificado na lista de atributos.
 5. Clique **em OK** para guardar.

![Adicionar atributo de origem ao esquema do conector AD AD Azure](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Passo 4: Criar uma regra de sincronização de entrada para fluir o valor do atributo a partir do Diretório Ativo no local
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem do diretório ativo para o metaverso:

1. Abra o Editor de Regras de Sincronização indo para **Iniciar o Editor** de  >  **Regras de Sincronização**.
2. Desa esta hora de **configurar** o filtro de pesquisa para a **entrada**.
3. Clique no **botão Adicionar nova regra** para criar uma nova regra de entrada.
4. No **separador Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Name | *Fornecer um nome* | Por exemplo, *In from AD – UserType* |
    | Description | *Fornecer uma descrição* |  |
    | Sistema Conectado | *Escolha o conector AD no local* |  |
    | Tipo de objeto de sistema conectado | **Utilizador** |  |
    | Tipo de objeto metaverso | **Pessoa** |  |
    | Tipo de ligação | **Join** |  |
    | Precedência | *Escolha um número entre 1 e 99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Vá ao **separador filtro de scoping** e adicione um **único grupo de filtro de scoping** com a seguinte cláusula:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | adminDDDDDDescrição | NOTSTARTWITH | Utilizador\_ |

    O filtro de deteção determina a que pontos no local a AD é aplicada esta regra de sincronização de entrada. Neste exemplo, utilizamos o mesmo filtro de deteção utilizado na regra de sincronização *in ad – User Common* out-of-box, que impede que a regra de sincronização seja aplicada a objetos do Utilizador criados através da funcionalidade de writeback do Utilizador Azure AD. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.

6. Vá ao separador **Transformação** e implemente a regra de transformação desejada. Por exemplo, se designou um atributo AD não reutilizado no local (como extensãoTribute1) como o atributo de origem para o UserType, pode implementar um fluxo de atributos diretos:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensãoAttribute1 | Desselecionado | Atualizar |

    Noutro exemplo, pretende obter o valor do atributo UserType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como Guest se o seu atributo De nome de utilizador AD no local terminar com a parte de domínio <em>@partners.fabrikam123.org</em> . Pode implementar uma expressão como esta:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Expression | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(L11(LCase([userPrincipalName])," @partners.fabrikam123.org ")=0),"Member","Guest"),Error("UserPrincipalName não está presente para determinar o UserType")) | Desselecionado | Atualizar |

7. Clique **em Adicionar** para criar a regra de entrada.

![Criar regra de sincronização de entrada](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Passo 5: Criar uma regra de sincronização de saída para fluir o valor do atributo para Azure AD
A regra de sincronização de saída permite que o valor do atributo flua do metaverso para o atributo UserType em Azure AD:

1. Vá ao Editor de Regras de Sincronização.
2. Desa esta hora o filtro de pesquisa **para** ser **de saída**.
3. Clique no **botão Adicionar nova regra.**
4. No **separador Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | Name | *Fornecer um nome* | Por exemplo, *out to AAD – UserType* |
    | Description | *Fornecer uma descrição* ||
    | Sistema Conectado | *Selecione o conector AAD* ||
    | Tipo de objeto de sistema conectado | **Utilizador** ||
    | Tipo de objeto metaverso | **Pessoa** ||
    | Tipo de ligação | **Join** ||
    | Precedência | *Escolha um número entre 1 e 99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Vá ao **separador filtro de scoping** e adicione um **único grupo de filtro de scoping** com duas cláusulas:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | IGUAL | User |
    | cloudMastered | NOTAQUAL | Verdadeiro |

    O filtro de deteção determina a que Azure AD se opõe a esta regra de sincronização de saída. Neste exemplo, utilizamos o mesmo filtro de deteção da regra de sincronização *out to AD – Identidade* do Utilizador fora da caixa. Impede que a regra de sincronização seja aplicada a objetos do Utilizador que não sejam sincronizados a partir do Diretório Ativo no local. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.

6. Vá ao separador **Transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Desselecionado | Atualizar |

7. Clique **em Adicionar** para criar a regra de saída.

![Criar regra de sincronização de saída](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Passo 6: Executar um ciclo completo de sincronização
Em geral, é necessário um ciclo completo de sincronização porque adicionámos novos atributos tanto ao Ative Directory como aos esquemas de conector AD Ad Azure, e introduzimos regras de sincronização personalizadas. Pretende verificar as alterações antes de exportá-las para a Azure AD. 

Pode utilizar os seguintes passos para verificar as alterações durante a execução manual dos passos que compõem um ciclo de sincronização completo.

1. Executar uma **importação completa** **no conector AD no local:**

   1. Aceda ao **separador Conectores** no Gestor de Serviço de Sincronização.
   2. Clique com o botão direito **no conector AD no local** e selecione **Executar**.
   3. Na caixa de diálogo pop-up, selecione **Full Import** e, em seguida, clique **em OK**.
   4. Espere que a operação termine.

      > [!NOTE]
      > Pode ignorar uma importação completa no conector AD no local se o atributo de origem já estiver incluído na lista de atributos importados. Por outras palavras, não teve de fazer quaisquer alterações durante [o Passo 2: Adicione o atributo de origem ao esquema de conector AD no local](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Executar uma **importação completa** no **Conector AD Azure:**

   1. Clique com o botão direito no **Conector AD Azure** e selecione **Executar**.
   2. Na caixa de diálogo pop-up, selecione **Full Import** e, em seguida, clique **em OK**.
   3. Espere que a operação termine.

3. Verifique as alterações da regra de sincronização num objeto do Utilizador existente:

    O atributo de origem do Ative Directy e do UserType da Azure AD foram importados para os respetivos Espaços de Conector. Antes de prosseguir com uma sincronização completa, faça uma **pré-visualização** num objeto do Utilizador existente no espaço do conector AD no local. O objeto que escolheu deve ter o atributo de origem povoado.
    
    Uma **pré-visualização** bem sucedida com o UserType povoado no metaverso é um bom indicador de que configuraste corretamente as regras de sincronização. Para obter informações sobre como fazer uma **pré-visualização,** consulte a secção [Verifique a alteração](#verify-the-change).

4. Executar uma **sincronização completa** **no conector AD no local:**

   1. Clique com o botão direito **no conector AD no local** e selecione **Executar**.
   2. Na caixa de diálogo pop-up, selecione **Full Synchronization** e, em seguida, clique **em OK**.
   3. Espere que a operação termine.

5. Verifique **as exportações pendentes** para a Azure AD:

   1. Clique com o botão direito no **Conector AD Azure** e selecione **Search Connector Space**.
   2. Na caixa de diálogo pop-up **do Espaço Do Conector search:**

      - Definir **âmbito** de **exportação pendente**.
      - Selecione as três caixas de verificação: **Adicionar,** **Modificar** e **Eliminar**.
      - Clique no botão **Procurar** para obter a lista de objetos com alterações a exportar. Para examinar as alterações para um determinado objeto, clique duas vezes no objeto.
      - Verifique se as alterações são esperadas.

6. **Executar Exportação** no **Conector AD Azure:**

   1. Clique com o botão direito no **Conector AD Azure** e selecione **Executar**.
   2. Na caixa de diálogo pop-up **Run Connector,** selecione **Export** e, em seguida, clique **em OK**.
   3. Aguarde a exportação para Azure AD para terminar.

> [!NOTE]
> Estes passos não incluem a sincronização completa e as etapas de exportação no Conector AD Azure. Estes passos não são necessários porque os valores do atributo estão a fluir do Ative Directory para o Azure apenas.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Passo 7: Re-activar o programador de sincronização
Re-activar o programador de sincronização incorporado:

1. Inicie uma sessão PowerShell.
2. Re-activar a sincronização programada executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true` .


## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração na [Compreensão declarativa.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Leia mais sobre a linguagem de expressão em [Compreensão Declarativas Expressões.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
