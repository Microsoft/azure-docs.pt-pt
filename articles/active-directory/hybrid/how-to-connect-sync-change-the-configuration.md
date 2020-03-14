---
title: 'Sincronização Azure AD Connect: Faça uma alteração de configuração no sincronizado Azure AD Connect'
description: Acompanha-o a fazer uma alteração na configuração da sincronização Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d77882817934d5ad98f16965aeb9dc246931c495
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261167"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Sincronização Azure AD Connect: Faça uma alteração na configuração padrão
O objetivo deste artigo é acompanhá-lo sobre como fazer alterações na configuração padrão no Diretório Ativo Azure (Azure AD) Ligar sincronização. Fornece passos para alguns cenários comuns. Com este conhecimento, deverá ser capaz de fazer alterações simples na sua própria configuração com base nas suas próprias regras de negócio.

> [!WARNING]
> Se fizer alterações às regras de sincronização fora da caixa, estas alterações serão substituídas da próxima vez que o Azure AD Connect for atualizado, resultando em resultados inesperados e prováveis de sincronização indesejados.
>
> As regras de sincronização fora da caixa têm uma impressão digital. Se fizeres uma alteração a estas regras, a impressão digital já não está a condizer. Poderá ter problemas no futuro quando tentar aplicar um novo lançamento do Azure AD Connect. Só faça alterações da forma como é descrito neste artigo.

## <a name="synchronization-rules-editor"></a>Editor de Regras de Sincronização
O Editor de Regras de Sincronização é usado para ver e alterar a configuração padrão. Pode encontrá-lo no menu **Iniciar** sob o grupo **Azure AD Connect.**  
![Iniciar o menu com](./media/how-to-connect-sync-change-the-configuration/startmenu2.png) de Editor de Regras de Sincronização

Quando abres o editor, vês as regras fora da caixa.

![Editor de Regras de Sincronização](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegar no editor
Usando as quedas no topo do editor, você pode rapidamente encontrar uma regra específica. Por exemplo, se quiser ver as regras em que os proxyAddresss do atributo estão incluídos, pode alterar as descidas para as seguintes:  
![Filtragem SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Para repor a filtragem e carregar uma nova configuração, prima F5 no teclado.

Na parte superior direita está o novo botão **de regra Adicionar.** Usa este botão para criar a sua própria regra personalizada.

Na parte inferior estão botões para agir com uma regra de sincronização selecionada. **Editar** e **Eliminar** faça o que espera. **Export** produz um script PowerShell para recriar a regra de sincronização. Com este procedimento, pode mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Crie a sua primeira regra personalizada
As alterações mais comuns são os fluxos de atributos. Os dados do seu diretório de origem podem não ser os mesmos que em Azure AD. No exemplo desta secção, certifique-se de que o nome dado de um utilizador está sempre em *caso adequado*.

### <a name="disable-the-scheduler"></a>Desativar o programador
O [programador](how-to-connect-sync-feature-scheduler.md) funciona a cada 30 minutos por defeito. Certifique-se de que não começa enquanto estiver a fazer alterações e a resolver problemas com as suas novas regras. Para desativar temporariamente o programador, inicie a PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Desativar o programador](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra
1. Clique **Em adicionar nova regra**.
2. Na página **Descrição,** insira o seguinte:  
   ![](./media/how-to-connect-sync-change-the-configuration/description2.png) de filtragem da regra de entrada  
   * **Nome**: Dar à regra um nome descritivo.
   * **Descrição**: Dê alguns esclarecimentos para que outra pessoa possa entender para que é a regra.
   * **Sistema Ligado**: Este é o sistema em que o objeto pode ser encontrado. Neste caso, selecione **Ative Directory Connector**.
   * **Sistema ligado/Tipo**de objeto metaverso : Selecione **Utilizador** e **Pessoa,** respectivamente.
   * **Tipo de ligação:** Altere este valor para **aderir**.
   * **Precedência**: Fornecer um valor único no sistema. Um valor numérico mais baixo indica maior precedência.
   * **Etiqueta**: Deixe isto vazio. Apenas as regras fora da caixa da Microsoft devem ter esta caixa povoada com um valor.
3. Na página do **filtro Scoping,** introduza **o nome dado ISNOTNULL**.  
   ![regras de deteção de regras de entrada](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Esta secção é utilizada para definir a que objetos a regra deve aplicar- se. Se ficar vazio, a regra aplicar-se-ia a todos os objetos do utilizador. No entanto, isso incluiria salas de conferências, contas de serviço e outros objetos de utilizador não-pessoas.
4. Na página de **regras de Join,** deixe o campo vazio.
5. Na página **Transformações,** altere **o FlowType** para **a Expressão**. Para **atribuir o alvo,** selecione **nome dado**. E para **Fonte,** insira **PCase([nome dado])** .
   ![transformações de regras de entrada](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   O motor de sincronização é sensível a casos tanto para o nome da função como para o nome do atributo. Se escrever algo errado, verá um aviso quando adiciona a regra. Podes salvar e continuar, mas tens de reabrir e corrigir a regra.
6. Clique em **Adicionar** para salvar a regra.

A sua nova regra personalizada deve ser visível com as outras regras de sincronização do sistema.

### <a name="verify-the-change"></a>Verifique a alteração
Com esta nova mudança, pretende certificar-se de que está a funcionar como esperado e que não está a cometer erros. Dependendo do número de objetos que tem, há duas maneiras de fazer este passo:

- Execute uma sincronização completa em todos os objetos.
- Executar uma pré-visualização e sincronizar completamente um único objeto.

Abra o Serviço de **Sincronização** a partir do menu **Iniciar.** Os passos nesta secção estão todos nesta ferramenta.

**Sincronização completa em todos os objetos**  

   1. Selecione **Conectores** na parte superior. Identifique o conector que alterou na secção anterior (neste caso, Ative Directory Domain Services) e selecione-o. 
   2. Para **ações,** selecione **Executar**.
   3. Selecione **Full Synchronization**e, em seguida, selecione **OK**.
   ![](./media/how-to-connect-sync-change-the-configuration/fullsync.png) de sincronização completa  
   Os objetos são agora atualizados no metaverso. Verifique as suas alterações olhando para o objeto no metaverso.

**Pré-visualização e sincronização completa num único objeto**  

   1. Selecione **Conectores** na parte superior. Identifique o conector que alterou na secção anterior (neste caso, Ative Directory Domain Services) e selecione-o.
   2. Selecione **Search Connector Space**. 
   3. Utilize o **Scope** para encontrar um objeto que deseja utilizar para testar a mudança. Selecione o objeto e clique em **Visualizar**. 
   4. No novo ecrã, selecione **'Pré-visualização'.**  
   ![Comprometer](./media/how-to-connect-sync-change-the-configuration/commitpreview.png) de pré-visualização  
   A mudança está agora comprometida com o metaverso.

**Veja o objeto no metaverso**  

1. Escolha alguns objetos de amostra para se certificar de que o valor é esperado e que a regra seja aplicada. 
2. Selecione **Metaverse Search** a partir do topo. Adicione qualquer filtro que precise para encontrar os objetos relevantes. 
3. Pelo resultado da pesquisa, abra um objeto. Olhe para os valores do atributo e verifique também na coluna **Regras de Sincronização** que a regra se aplicava como esperado.  
![Pesquisa de metaversos](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Ativar o programador
Se tudo estiver como esperado, pode voltar a ativar o programador. Da PowerShell, corra `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações comuns do fluxo de atributos
A secção anterior descrevia como fazer alterações num fluxo de atributos. Nesta secção, são dados alguns exemplos adicionais. Os passos para como criar a regra de sincronização são abreviados, mas você pode encontrar os passos completos na secção anterior.

### <a name="use-an-attribute-other-than-the-default"></a>Use um atributo diferente do padrão
Neste cenário de Fabrikam, há uma floresta onde o alfabeto local é usado para o nome, apelido e nome de exibição. A representação de caracteres latinos destes atributos pode ser encontrada nos atributos de extensão. Para a construção de uma lista global de endereços em Azure AD e Office 365, a organização quer usar estes atributos em vez disso.

Com uma configuração padrão, um objeto da floresta local é assim:  
![Fluxo de atributo 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, faça o seguinte:

1. Abra o Editor de Regras de **Sincronização** do menu **Iniciar.**
2. Com a **Entrada** ainda selecionada para a esquerda, clique no novo botão de **regra Adicionar.**
3. Dê à regra um nome e uma descrição. Selecione a instância de Diretório Ativo no local e os tipos de objetos relevantes. No **Tipo de Link,** selecione **Juntar**. Para **Precedência,** escolha um número que não seja utilizado por outra regra. As regras fora da caixa começam com 100, pelo que o valor 50 pode ser usado neste exemplo.
  fluxo de atributo de ![2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Deixe **o filtro de deteção** vazio. (Isto é, deve aplicar-se a todos os objetos de utilizador na floresta.)
5. Deixe **as regras de adesão** vazias. (Isto é, deixe a regra fora da caixa lidar com qualquer junta.)
6. Em **Transformações,** crie os seguintes fluxos:  
  fluxo de atributo ![3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Clique em **Adicionar** para salvar a regra.
8. Vá ao Gestor de Serviços de **Sincronização.** Nos **Conectores,** selecione o conector onde adicionou a regra. Selecione **Executar**, e, em seguida, selecione **Full Synchronization**. Uma sincronização completa recalcula todos os objetos utilizando as regras atuais.

Este é o resultado para o mesmo objeto com esta regra personalizada:  
![Fluxo de atributo 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento dos atributos
Os atributos de cordas são indexáveis por padrão, e o comprimento máximo é de 448 caracteres. Se estiver a trabalhar com atributos de cadeia que possam conter mais, certifique-se de incluir o seguinte no fluxo de atributos:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Alteração do utilizadorPrincipalSuffix
O atributo do userPrincipalName no Ative Directory nem sempre é conhecido pelos utilizadores e pode não ser adequado como id de entrada. Com o assistente de instalação de sincronização Azure AD Connect, pode escolher um atributo diferente- por exemplo, *correio*. Mas em alguns casos, o atributo deve ser calculado.

Por exemplo, a empresa Contoso tem dois diretórios Azure AD, um para produção e outro para testes. Eles querem que os utilizadores no seu inquilino de teste utilizem outro sufixo no ID de inscrição:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Nesta expressão, pegue tudo à esquerda do primeiro @-sign (Palavra) e concatena com uma corda fixa.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Converter um atributo de vários valores para um valor único
Alguns atributos no Diretório Ativo são multivalorizados no esquema, embora pareçam de valor único em Utilizadores e Computadores de Diretório Ativo. Um exemplo é o atributo de descrição:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Nesta expressão, se o atributo tiver um valor, pegue o primeiro item (*Item*) no atributo, remova os espaços de liderança e de trailing (*Trim),* e, em seguida, mantenha os primeiros 448 caracteres *(À esquerda)* na corda.

### <a name="do-not-flow-an-attribute"></a>Não flua um atributo
Para obter informações sobre o cenário desta secção, consulte Controlar o processo de [fluxo de atributos](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Há duas maneiras de não fluir um atributo. A primeira é utilizando o assistente de instalação para [remover os atributos selecionados](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Esta opção funciona se nunca tiver sincronizado o atributo antes. No entanto, se começou a sincronizar este atributo e, mais tarde, removê-lo com esta funcionalidade, o motor sincronizado para de gerir o atributo e os valores existentes são deixados em Azure AD.

Se quiser remover o valor de um atributo e certificar-se de que não flui no futuro, precisa de criar uma regra personalizada.

Neste cenário de Fabrikam, percebemos que alguns dos atributos que sincronizamos com a nuvem não deveriam estar lá. Queremos garantir que estes atributos sejam removidos do Azure AD.  
![Maus atributos de extensão](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Crie uma nova regra de sincronização de entrada e povoe a descrição.
  ![Descrições](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Criar fluxos de atributos com **Expressão** para **FlowType** e com **AuthoritativeNull** for **Source**. O **Autor** literal Null indica que o valor deve estar vazio no metaverso, mesmo que uma regra de sincronização de menor precedência tente povoar o valor.
  Transformação ![para atributos de extensão](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Salve a regra da sincronização. Inicie o **Serviço de Sincronização,** encontre o conector, selecione **Executar,** e, em seguida, selecione **Full Synchronization**. Este passo recalcula todos os fluxos de atributos.
4. Verifique se as alterações previstas estão prestes a ser exportadas através da pesquisa no Espaço do Conector.
  ![encenação apagar](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Criar regras com a PowerShell
Usar a regra de sincronização o editor funciona bem quando só tem algumas alterações a fazer. Se precisar de fazer muitas alterações, o PowerShell pode ser uma opção melhor. Algumas funcionalidades avançadas só estão disponíveis com o PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obtenha o script PowerShell para uma regra fora da caixa
Para ver o script PowerShell que criou uma regra fora da caixa, selecione a regra no editor de regras de sincronização e clique em **Exportar**. Esta ação dá-lhe o script PowerShell que criou a regra.

### <a name="advanced-precedence"></a>Precedência avançada
As regras de sincronização fora da caixa começam com um valor de precedência de 100. Se você tem muitas florestas e precisa fazer muitas mudanças personalizadas, então 99 regras de sincronização podem não ser suficientes.

Pode instruir o motor de sincronização que pretende inserir regras adicionais antes das regras fora da caixa. Para obter este comportamento, siga estes passos:

1. Marque a primeira regra de sincronização fora da caixa **(In from AD-User Join)** no editor de regras de sincronização e selecione **Export**. Copie o valor do identificador SR.  
![PowerShell antes de mudar](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Crie a nova regra de sincronização. Pode usar o editor de regras de sincronização para criá-lo. Exporte a regra para um guião powerShell.
3. Na propriedade **PrecedênciaS Antes,** insira o valor do Identificador a partir da regra fora da caixa. Definir a **Precedência** para **0**. Certifique-se de que o atributo identificador é único e que não está a reutilizar um GUID de outra regra. Certifique-se também de que a propriedade **ImmutableTag** não está definida. Esta propriedade deve ser definida apenas para uma regra fora da caixa.
4. Guarde o script PowerShell e execute-o. O resultado é que a sua regra personalizada é atribuída ao valor de precedência de 100 e todas as outras regras fora da caixa são incrementadas.  
![PowerShell após mudança](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Pode ter muitas regras de sincronização personalizadas usando o mesmo **valor PrecedênciaAntes** quando necessário.

## <a name="enable-synchronization-of-usertype"></a>Ativar a sincronização do UserType
O Azure AD Connect suporta a sincronização do atributo **userType** para objetos **do Utilizador** na versão 1.1.524.0 e posteriormente. Mais especificamente, foram introduzidas as seguintes alterações:

- O esquema do tipo de objeto **Utilizador** no Conector AD Azure é estendido para incluir o atributo UserType, que é da cadeia do tipo e é de valor único.
- O esquema do tipo objeto **Pessoa** no metaverso é estendido para incluir o atributo UserType, que é da cadeia do tipo e é de valor único.

Por predefinição, o atributo userType não está ativado para sincronização porque não existe um atributo do UserType correspondente no Diretório Ativo no local. Tem de ativar manualmente a sincronização. Antes de o fazer, deve tomar nota do seguinte comportamento imposto pela Azure AD:

- A Azure AD só aceita dois valores para o atributo UserType: **Membro** e **Convidado**.
- Se o atributo userType não estiver ativado para sincronização no Azure AD Connect, os utilizadores de AD Azure criados através da sincronização do diretório teriam o atributo userType definido para **Membro**.
- A Azure AD não permite que o atributo userType sobre os utilizadores de AD Azure existentes seja alterado pelo Azure AD Connect. Só pode ser definido durante a criação dos utilizadores da AD Azure e alterado através da [Powershell](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0).

Antes de permitir a sincronização do atributo UserType, deve primeiro decidir como o atributo é derivado do Diretório Ativo no local. Seguem-se as abordagens mais comuns:

- Designe um atributo AD não utilizado no local (como extensãoAttribute1) a ser usado como atributo de origem. O atributo ad designado no local deve ser da **cadeia**tipo, ser de valor único e conter o valor **membro** ou **hóspede**. 

    Se escolher esta abordagem, deve certificar-se de que o atributo designado é preenchido com o valor correto para todos os objetos de utilizador existentes no diretório ativo no local que são sincronizados com a AD Azure antes de permitir a sincronização do atributo UserType .

- Em alternativa, pode obter o valor para o atributo userType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como **Guest** se o seu atributo de utilizador adprincipalname no local terminar com parte de domínio <em>@partners.fabrikam123.org</em>. 

    Como mencionado anteriormente, o Azure AD Connect não permite que o atributo userType sobre os utilizadores ad ad existentes seja alterado pelo Azure AD Connect. Por isso, deve garantir que a lógica que decidiu é consistente com a forma como o atributo userType já está configurado para todos os utilizadores de Anúncios Azure existentes no seu inquilino.

Os passos para permitir a sincronização do atributo UserType podem ser resumidos como:

1.  Desative o programador de sincronização e verifique se não há sincronização em curso.
2.  Adicione o atributo de origem ao esquema de conector AD no local.
3.  Adicione o UserType ao esquema do Conector AD Azure.
4.  Crie uma regra de sincronização de entrada para fluir o valor do atributo a partir do diretório ativo no local.
5.  Crie uma regra de sincronização de saída para fluir o valor do atributo para a AD Azure.
6.  Executar um ciclo de sincronização completa.
7.  Ative o programador de sincronização.

>[!NOTE]
> O resto desta secção cobre estes passos. São descritos no contexto de uma implantação da AD Azure com topologia uniflorestal e sem regras de sincronização personalizadas. Se tiver topologia multi-floresta, regras de sincronização personalizadas configuradas ou tiver um servidor de encenação, precisa de ajustar os passos em conformidade.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desative o programador de sincronização e verifique se não há sincronização em curso
Para evitar a exportação de alterações não intencionais para a AD Azure, certifique-se de que não ocorre sincronização enquanto estiver no meio da atualização das regras de sincronização. Para desativar o programador de sincronização incorporado:

 1. Inicie uma sessão powerShell no servidor Azure AD Connect.
 2. Desative a sincronização programada executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Abra o Gestor de Serviços de Sincronização **indo iniciar** > **Serviço de Sincronização.**
 4. Vá ao separador **Operações** e confirme que não há nenhuma operação com um estado em *curso*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Passo 2: Adicione o atributo de origem ao esquema de conector AD no local
Nem todos os atributos Da AD Azure são importados para o espaço de conector AD no local. Para adicionar o atributo de origem à lista dos atributos importados:

 1. Vá ao separador **Conectores** no Gestor de Serviçode Sincronização.
 2. Clique no conector AD no local e selecione **Propriedades**.
 3. Na caixa de diálogo pop-up, aceda ao separador **Select Atributos.**
 4. Certifique-se de que o atributo de origem é verificado na lista de atributos.
 5. Clique **em OK** para guardar.
![Adicionar atributo de origem ao conector ad-in-local schema](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Passo 3: Adicione o UserType ao esquema do conector Azure AD
Por predefinição, o atributo userType não é importado para o Espaço de Ligação AD Azure. Para adicionar o atributo userType à lista de atributos importados:

 1. Vá ao separador **Conectores** no Gestor de Serviçode Sincronização.
 2. Clique no **Conector Azure AD** e selecione **Propriedades**.
 3. Na caixa de diálogo pop-up, aceda ao separador **Select Atributos.**
 4. Certifique-se de que o atributo userType está verificado na lista de atributos.
 5. Clique **em OK** para guardar.

![Adicione atributo de origem ao esquema do Conector Azure AD](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Passo 4: Criar uma regra de sincronização de entrada para fluir o valor do atributo a partir do diretório ativo no local
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem do Diretório Ativo no local para o metaverso:

1. Abra o Editor de Regras de Sincronização **indo iniciar** > Editor de Regras de **Sincronização.**
2. Desloque o filtro de procura **Direção** para estar **a caminho**de entrada .
3. Clique no novo botão de **regra Adicionar** para criar uma nova regra de entrada.
4. Sob o separador **Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Forneça um nome* | Por exemplo, *dentro de AD – User UserType* |
    | Descrição | *Fornecer uma descrição* |  |
    | Sistema Conectado | *Escolha o conector AD no local* |  |
    | Tipo de objeto de sistema conectado | **Utilizador** |  |
    | Tipo de objeto metaverso | **Pessoa** |  |
    | Tipo de ligação | **Associar** |  |
    | Precedência | *Escolha um número entre 1-99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Vá ao separador **de filtro Scoping** e adicione um único grupo de filtros de **deteção** com a seguinte cláusula:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | adminDescription | NÃO COMEÇAR COM | \_ de utilização |

    O filtro de deteção determina a que se aplica a DA no local esta regra de sincronização de entrada. Neste exemplo, utilizamos o mesmo filtro de digitalização utilizado na regra de sincronização fora da caixa Do *Utilizador Comum,* que impede que a regra de sincronização seja aplicada aos objetos do Utilizador criados através da funcionalidade de ressurgimento do Utilizador AD Azure. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.

6. Vá ao separador **Transformação** e implemente a regra de transformação desejada. Por exemplo, se designou um atributo AD não utilizado no local (como extensãoAttribute1) como atributo de origem para o UserType, pode implementar um fluxo de atributo direto:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Direct | Tipo de utilizador | extensãoAtribuir1 | Sem controlo | Atualizar |

    Noutro exemplo, pretende obter o valor para o atributo userType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como Guest se o seu atributo de adia no local termina com parte de domínio <em>@partners.fabrikam123.org</em>. Pode implementar uma expressão como esta:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Expressão | Tipo de utilizador | IIF (IsPresent([userprincipalname]), IIF(CBool(LCase([userprincipalName])"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName não está presente para determinar o UtilizadorType")) | Sem controlo | Atualizar |

7. Clique em **Adicionar** para criar a regra de entrada.

![Criar regra de sincronização de entrada](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Passo 5: Criar uma regra de sincronização de saída para fluir o valor do atributo para a AD Azure
A regra de sincronização de saída permite que o valor do atributo flua do metaverso para o atributo UserType em Azure AD:

1. Vá ao Editor de Regras de Sincronização.
2. Desloque o filtro de procura **Direction** a **outbound**.
3. Clique no novo botão de **regra Adicionar.**
4. Sob o separador **Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | Nome | *Forneça um nome* | Por exemplo, *out to AAD – User UserType* |
    | Descrição | *Fornecer uma descrição* ||
    | Sistema Conectado | *Selecione o conector AAD* ||
    | Tipo de objeto de sistema conectado | **Utilizador** ||
    | Tipo de objeto metaverso | **Pessoa** ||
    | Tipo de ligação | **Associar** ||
    | Precedência | *Escolha um número entre 1-99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Vá ao separador **de filtro Scoping** e adicione um único grupo de filtros de **deteção** com duas cláusulas:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilizador |
    | cloudMastered | NOTEQUAL | Verdadeiro |

    O filtro de deteção determina a que objetos Da AD Azure esta regra de sincronização de saída é aplicada. Neste exemplo, utilizamos o mesmo filtro de digitalização da regra de sincronização out *to AD – Identidade* do Utilizador fora da caixa. Impede que a regra de sincronização seja aplicada a objetos do Utilizador que não sejam sincronizados a partir do Diretório Ativo no local. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.

6. Vá ao separador **Transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Direct | Tipo de utilizador | Tipo de utilizador | Sem controlo | Atualizar |

7. Clique em **Adicionar** para criar a regra de saída.

![Criar regra de sincronização de saída](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Passo 6: Executar um ciclo de sincronização completa
Em geral, é necessário um ciclo de sincronização completo porque adicionámos novos atributos tanto ao Diretório Ativo como aos schemas de Conector AD Azure, e introduzimos regras de sincronização personalizadas. Deverá verificar as alterações antes de as exportar para a Azure AD. 

Pode utilizar os seguintes passos para verificar as alterações durante a execução manual dos passos que compõem um ciclo de sincronização completa.

1. Executar uma **importação completa** no **conector AD no local:**

   1. Vá ao separador **Operações** no Gestor de Serviços de Sincronização.
   2. Clique no **conector AD no local** e selecione **Executar**.
   3. Na caixa de diálogo pop-up, selecione **Full Import** e, em seguida, clique em **OK**.
   4. Espere que a operação termine.

      > [!NOTE]
      > Pode saltar uma importação completa no Conector AD no local se o atributo de origem já estiver incluído na lista de atributos importados. Por outras palavras, não foi necessário efetuar alterações durante o [passo 2: Adicione o atributo de origem ao esquema de conector AD no local](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Executar uma **importação completa** no **Conector Azure AD:**

   1. Clique no **Conector Azure AD** e selecione **Executar**.
   2. Na caixa de diálogo pop-up, selecione **Full Import** e, em seguida, clique em **OK**.
   3. Espere que a operação termine.

3. Verifique se a regra de sincronização muda num objeto utilizador existente:

    O atributo de origem do Diretório Ativo no local e do UserType da Azure AD foram importados para os respetivos Espaços de Conector. Antes de prosseguir com uma sincronização completa, faça uma **Pré-visualização** de um objeto utilizador existente no espaço de conector AD no local. O objeto que escolheu deve ter o atributo de origem povoado.
    
    Uma **pré-visualização** bem sucedida com o UserType povoada no metaverso é um bom indicador de que configura corretamente as regras de sincronização. Para obter informações sobre como fazer uma **Pré-visualização,** consulte a secção [Verifique a alteração](#verify-the-change).

4. Executar uma **sincronização completa** no **conector AD no local:**

   1. Clique no **conector AD no local** e selecione **Executar**.
   2. Na caixa de diálogo pop-up, selecione **Full Synchronization** e, em seguida, clique EM **OK**.
   3. Espere que a operação termine.

5. Verificar **as exportações pendentes** para a AD Azure:

   1. Clique no **Conector Azure AD** e selecione **Search Connector Space**.
   2. Na caixa de diálogo pop-up **do Connector Space:**

      - Definir **o âmbito** para a **exportação pendente.**
      - Selecione as três caixas de verificação: **Adicionar,** **Modificar**e **Eliminar**.
      - Clique no botão **'Procurar'** para obter a lista de objetos com alterações a exportar. Para examinar as alterações para um determinado objeto, clique duas vezes no objeto.
      - Verifique se as alterações são esperadas.

6. Executar **a exportação** no **Conector Azure AD:**

   1. Clique no **Conector Azure AD** e selecione **Executar**.
   2. Na caixa de diálogo pop-up **Run Connector,** selecione **Exportar** e, em seguida, clique **EM OK**.
   3. Espere a exportação para AD Azure para terminar.

> [!NOTE]
> Estes passos não incluem a sincronização completa e os passos de exportação no Conector AD Azure. Estes passos não são necessários porque os valores do atributo estão fluindo do diretório ativo no local apenas para a AD Azure.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Passo 7: Reativar o programador de sincronização
Reativar o programador de sincronização incorporado:

1. Inicie uma sessão powerShell.
2. Reativar a sincronização programada executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração em [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão na compreensão das expressões de [disposição declarativas.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
