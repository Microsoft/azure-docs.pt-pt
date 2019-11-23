---
title: 'Sincronização de Azure AD Connect: fazer uma alteração de configuração no Azure AD Connect sincronização | Microsoft Docs'
description: Explica como fazer uma alteração na configuração no Azure AD Connect sincronização.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5844d440da768ae2647ea7f15c4c913f83078ce1
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672958"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Sincronização de Azure AD Connect: faça uma alteração na configuração padrão
A finalidade deste artigo é orientá-lo sobre como fazer alterações na configuração padrão na sincronização do Azure Active Directory (Azure AD) Connect. Ele fornece etapas para alguns cenários comuns. Com esse conhecimento, você deve ser capaz de fazer alterações simples em sua própria configuração com base em suas próprias regras de negócios.

> [!WARNING]
> Se você fizer alterações nas regras de sincronização prontas para uso padrão, essas alterações serão substituídas na próxima vez que o Azure AD Connect for atualizado, resultando em resultados de sincronização indesejados e prováveis.
>
> As regras de sincronização integradas padrão têm uma impressão digital. Se você fizer uma alteração nessas regras, a impressão digital não será mais compatível. Você pode ter problemas no futuro ao tentar aplicar uma nova versão do Azure AD Connect. Faça alterações apenas da maneira descrita neste artigo.

## <a name="synchronization-rules-editor"></a>Editor de regras de sincronização
O editor de regras de sincronização é usado para ver e alterar a configuração padrão. Você pode encontrá-lo no menu **Iniciar** sob o grupo de **Azure ad Connect** .  
![menu iniciar com o editor de regra de sincronização](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Ao abrir o editor, você verá as regras prontas para uso padrão.

![Editor de regra de sincronização](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegando no editor
Usando os menus suspensos na parte superior do editor, você pode encontrar rapidamente uma regra específica. Por exemplo, se você quiser ver as regras em que o atributo proxyAddresses está incluído, você pode alterar os menus suspensos para o seguinte:  
![Filtragem de SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Para redefinir a filtragem e carregar uma nova configuração, pressione F5 no teclado.

No canto superior direito está o botão **Adicionar nova regra** . Use esse botão para criar sua própria regra personalizada.

Na parte inferior estão os botões para atuar em uma regra de sincronização selecionada. **Editar** e **excluir** fazem o que você espera. A **exportação** produz um script do PowerShell para recriar a regra de sincronização. Com esse procedimento, você pode mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Criar sua primeira regra personalizada
As alterações mais comuns são nos fluxos de atributo. Os dados no diretório de origem podem não ser os mesmos do Azure AD. No exemplo nesta seção, verifique se o nome fornecido de um usuário está sempre no *caso adequado*.

### <a name="disable-the-scheduler"></a>Desabilitar o Agendador
O [Agendador](how-to-connect-sync-feature-scheduler.md) é executado a cada 30 minutos por padrão. Verifique se ele não está iniciando enquanto você está fazendo alterações e Solucionando problemas de suas novas regras. Para desabilitar temporariamente o Agendador, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Desabilitar o Agendador](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra
1. Clique em **Adicionar nova regra**.
2. Na página **Descrição** , insira o seguinte:  
   ![filtragem de regra de entrada](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Nome**: dê um nome descritivo à regra.
   * **Descrição**: dê algum esclarecimento para que outra pessoa possa entender o que é a regra.
   * **Sistema conectado**: esse é o sistema no qual o objeto pode ser encontrado. Nesse caso, selecione **conector de Active Directory**.
   * **Tipo de objeto do sistema/metaverso conectado**: selecione **usuário** e **pessoa**, respectivamente.
   * **Tipo de link**: altere esse valor para **Join**.
   * **Precedência**: forneça um valor que seja exclusivo no sistema. Um valor numérico inferior indica maior precedência.
   * **Marca**: deixe em branco. Somente as regras prontas para uso da Microsoft devem ter essa caixa preenchida com um valor.
3. Na página **filtro de escopo** , insira o **ISNOTNULL especificado**.  
   ![filtro de escopo de regra de entrada](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Esta seção é usada para definir a quais objetos a regra deve ser aplicada. Se for deixado em branco, a regra se aplicará a todos os objetos de usuário. No entanto, isso incluiria salas de conferência, contas de serviço e outros objetos de usuário que não são de pessoas.
4. Na página **regras de junção** , deixe o campo vazio.
5. Na página **transformações** , altere **flowtype** para **expressão**. Para **atributo de destino**, selecione o **especificado**. E para **origem**, insira **PCase ([excertoname])** .
   ![transformações de regra de entrada](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   O mecanismo de sincronização diferencia maiúsculas de minúsculas para o nome da função e o nome do atributo. Se você digitar algo errado, verá um aviso ao adicionar a regra. Você pode salvar e continuar, mas precisa reabrir e corrigir a regra.
6. Clique em **Adicionar** para salvar a regra.

Sua nova regra personalizada deve ser visível com as outras regras de sincronização no sistema.

### <a name="verify-the-change"></a>Verificar a alteração
Com essa nova alteração, você deseja verificar se ela está funcionando conforme o esperado e não está gerando erros. Dependendo do número de objetos que você tem, há duas maneiras de fazer essa etapa:

- Execute uma sincronização completa em todos os objetos.
- Execute uma visualização e uma sincronização completa em um único objeto.

Abra o **serviço de sincronização** no menu **Iniciar** . As etapas nesta seção estão todas nesta ferramenta.

**Sincronização completa em todos os objetos**  

   1. Selecione **conectores** na parte superior. Identifique o conector que você alterou na seção anterior (nesse caso, Active Directory Domain Services) e selecione-o. 
   2. Para **ações**, selecione **executar**.
   3. Selecione **sincronização completa**e, em seguida, selecione **OK**.
   ![](./media/how-to-connect-sync-change-the-configuration/fullsync.png) de sincronização completa  
   Os objetos agora são atualizados no metaverso. Verifique as alterações examinando o objeto no metaverso.

**Visualização e sincronização completa em um único objeto**  

   1. Selecione **conectores** na parte superior. Identifique o conector que você alterou na seção anterior (nesse caso, Active Directory Domain Services) e selecione-o.
   2. Selecione **Pesquisar espaço do conector**. 
   3. Use **escopo** para localizar um objeto que você deseja usar para testar a alteração. Selecione o objeto e clique em **Visualizar**. 
   4. Na nova tela, selecione **visualização de confirmação**.  
   ](./media/how-to-connect-sync-change-the-configuration/commitpreview.png) de visualização de confirmação ![  
   A alteração agora está confirmada no metaverso.

**Exibir o objeto no metaverso**  

1. Escolha alguns objetos de exemplo para certificar-se de que o valor é esperado e que a regra foi aplicada. 
2. Selecione **pesquisa de metaverso** na parte superior. Adicione qualquer filtro que você precisa para localizar os objetos relevantes. 
3. No resultado da pesquisa, abra um objeto. Examine os valores de atributo e verifique também na coluna **regras de sincronização** que a regra aplicou conforme o esperado.  
![Pesquisa de metaversos](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Habilitar o Agendador
Se tudo estiver conforme o esperado, você poderá habilitar o Agendador novamente. No PowerShell, execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações de fluxo de atributo comuns
A seção anterior descreveu como fazer alterações em um fluxo de atributos. Nesta seção, são fornecidos alguns exemplos adicionais. As etapas para criar a regra de sincronização são abreviadas, mas você pode encontrar as etapas completas na seção anterior.

### <a name="use-an-attribute-other-than-the-default"></a>Usar um atributo diferente do padrão
Neste cenário da Fabrikam, há uma floresta em que o alfabeto local é usado para determinado nome, sobrenome e nome de exibição. A representação de caractere latino desses atributos pode ser encontrada nos atributos de extensão. Para criar uma lista de endereços global no Azure AD e no Office 365, a organização deseja usar esses atributos em vez disso.

Com uma configuração padrão, um objeto da floresta local tem esta aparência:  
![Fluxo de atributos 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, faça o seguinte:

1. Abra o **Editor de regras de sincronização** no menu **Iniciar** .
2. Com a **entrada** ainda selecionada à esquerda, clique no botão **Adicionar nova regra** .
3. Dê um nome e uma descrição à regra. Selecione a instância de Active Directory local e os tipos de objeto relevantes. Em **tipo de link**, selecione **ingressar**. Para **precedência**, escolha um número que não seja usado por outra regra. As regras prontas para uso começam com 100, portanto, o valor 50 pode ser usado neste exemplo.
  ![fluxo de atributos 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Deixe **filtro de escopo** vazio. (Ou seja, ele deve se aplicar a todos os objetos de usuário na floresta.)
5. Deixe **as regras de junção** vazias. (Ou seja, deixe que a regra pronta para uso Manipule as junções.)
6. Em **transformações**, crie os seguintes fluxos:  
  fluxo de atributos do ![3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Clique em **Adicionar** para salvar a regra.
8. Vá para **Synchronization Service Manager**. Em **conectores**, selecione o conector ao qual você adicionou a regra. Selecione **executar**e, em seguida, selecione **sincronização completa**. Uma sincronização completa recalcula todos os objetos usando as regras atuais.

Este é o resultado do mesmo objeto com esta regra personalizada:  
![Fluxo de atributos 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento dos atributos
Os atributos de cadeia de caracteres são indexáveis por padrão e o comprimento máximo é de 448 caracteres. Se você estiver trabalhando com atributos de cadeia de caracteres que podem conter mais, certifique-se de incluir o seguinte no fluxo de atributos:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Alterando o userPrincipalSuffix
O atributo userPrincipalName em Active Directory nem sempre é conhecido pelos usuários e pode não ser adequado como a ID de entrada. Com o assistente de instalação do Azure AD Connect Sync, você pode escolher um atributo diferente, por exemplo, *email*. Mas, em alguns casos, o atributo deve ser calculado.

Por exemplo, a empresa contoso tem dois diretórios do Azure AD, um para produção e outro para teste. Eles querem que os usuários em seu locatário de teste usem outro sufixo na ID de entrada:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Nessa expressão, faça tudo o que resta da primeira @-sign (Word) e concatene com uma cadeia de caracteres fixa.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Converter um atributo de vários valores em um único valor
Alguns atributos em Active Directory têm valores múltiplos no esquema, mesmo que pareçam um valor único em Active Directory usuários e computadores. Um exemplo é o atributo de descrição:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Nessa expressão, se o atributo tiver um valor, pegue o primeiro item (*Item*) no atributo, remova espaços à esquerda e à direita (*Trim*) e, em seguida, mantenha os primeiros 448 caracteres (*à esquerda*) na cadeia de caracteres.

### <a name="do-not-flow-an-attribute"></a>Não fluir um atributo
Para obter informações sobre o cenário desta seção, consulte [controlar o processo de fluxo de atributo](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Há duas maneiras de não fluir um atributo. A primeira é usar o assistente de instalação para [remover os atributos selecionados](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Essa opção funcionará se você nunca tiver sincronizado o atributo antes. No entanto, se você tiver iniciado a sincronização desse atributo e removê-lo posteriormente com esse recurso, o mecanismo de sincronização parará de gerenciar o atributo e os valores existentes serão deixados no Azure AD.

Se você quiser remover o valor de um atributo e certificar-se de que ele não flua no futuro, você precisará criar uma regra personalizada.

Neste cenário da Fabrikam, percebemos que alguns dos atributos que sincronizamos com a nuvem não devem estar lá. Queremos garantir que esses atributos sejam removidos do Azure AD.  
![Atributos de extensão inválidos](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Crie uma nova regra de sincronização de entrada e preencha a descrição.
  Descrições de ![](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Crie fluxos de atributo com **expressão** para **flowtype** e com **AuthoritativeNull** para **origem**. O **AuthoritativeNull** literal indica que o valor deve estar vazio no metaverso, mesmo que uma regra de sincronização de precedência inferior Tente preencher o valor.
  Transformação de ![para atributos de extensão](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Salve a regra de sincronização. Inicie o **serviço de sincronização**, localize o conector, selecione **executar**e, em seguida, selecione **sincronização completa**. Esta etapa recalcula todos os fluxos de atributo.
4. Verifique se as alterações pretendidas estão prestes a ser exportadas pesquisando o espaço do conector.
  ](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png) de exclusão ![preparado

## <a name="create-rules-with-powershell"></a>Criar regras com o PowerShell
O uso do editor de regra de sincronização funciona bem quando você tem apenas algumas alterações a serem feitas. Se você precisar fazer muitas alterações, o PowerShell poderá ser uma opção melhor. Alguns recursos avançados estão disponíveis apenas com o PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obter o script do PowerShell para uma regra integrada
Para ver o script do PowerShell que criou uma regra pronta para uso, selecione a regra no editor de regras de sincronização e clique em **Exportar**. Essa ação fornece o script do PowerShell que criou a regra.

### <a name="advanced-precedence"></a>Precedência avançada
As regras de sincronização integradas começam com um valor de precedência de 100. Se você tiver muitas florestas e precisar fazer muitas alterações personalizadas, as regras de sincronização 99 podem não ser suficientes.

Você pode instruir o mecanismo de sincronização que deseja que as regras adicionais sejam inseridas antes das regras prontas para uso. Para obter esse comportamento, siga estas etapas:

1. Marque a primeira regra de sincronização pronta para uso (**entrada do AD – ingressar no usuário**) no editor de regras de sincronização e selecione **Exportar**. Copie o valor do identificador SR.  
![PowerShell antes de alterar](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Crie a nova regra de sincronização. Você pode usar o editor de regras de sincronização para criá-lo. Exporte a regra para um script do PowerShell.
3. Na propriedade **PrecedenceBefore**, insira o valor do identificador da regra pronta para uso. Defina a **precedência** como **0**. Verifique se o atributo do identificador é exclusivo e se você não está Reutilizando um GUID de outra regra. Verifique também se a propriedade **ImmutableTag** não está definida. Essa propriedade deve ser definida somente para uma regra integrada.
4. Salve o script do PowerShell e execute-o. O resultado é que sua regra personalizada recebe o valor de precedência de 100 e todas as outras regras prontas para uso são incrementadas.  
![PowerShell após a alteração](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Você pode ter muitas regras de sincronização personalizadas usando o mesmo valor de **PrecedenceBefore** quando necessário.

## <a name="enable-synchronization-of-usertype"></a>Habilitar a sincronização de UserType
Azure AD Connect dá suporte à sincronização do atributo **UserType** para objetos de **usuário** na versão 1.1.524.0 e posterior. Mais especificamente, as seguintes alterações foram introduzidas:

- O esquema do tipo de objeto **usuário** no Azure ad Connector é estendido para incluir o atributo UserType, que é do tipo cadeia de caracteres e é de valor único.
- O esquema do tipo de objeto **Person** no metaverso é estendido para incluir o atributo UserType, que é do tipo cadeia de caracteres e é de valor único.

Por padrão, o atributo UserType não está habilitado para sincronização porque não há nenhum atributo UserType correspondente no Active Directory local. Você deve habilitar a sincronização manualmente. Antes de fazer isso, você deve anotar o seguinte comportamento imposto pelo Azure AD:

- O Azure AD aceita apenas dois valores para o atributo UserType: **membro** e **convidado**.
- Se o atributo UserType não estiver habilitado para sincronização no Azure AD Connect, os usuários do Azure AD criados por meio da sincronização de diretório teriam o atributo UserType definido como **membro**.
- O Azure AD não permite que o atributo UserType em usuários existentes do Azure AD seja alterado por Azure AD Connect. Ele só pode ser definido durante a criação dos usuários do Azure AD.

Antes de habilitar a sincronização do atributo UserType, primeiro você deve decidir como o atributo é derivado do Active Directory local. Estas são as abordagens mais comuns:

- Designe um atributo do AD local não utilizado (como extensionAttribute1) a ser usado como o atributo de origem. O atributo do AD local designado deve ser do tipo cadeia de **caracteres**, ter um valor único e conter o **membro** ou o **convidado**. 

    Se você escolher essa abordagem, deverá garantir que o atributo designado seja preenchido com o valor correto para todos os objetos de usuário existentes no Active Directory local que são sincronizados com o Azure AD antes de habilitar a sincronização do atributo UserType .

- Como alternativa, você pode derivar o valor para o atributo UserType de outras propriedades. Por exemplo, você deseja sincronizar todos os usuários como **convidados** se seu atributo USERPRINCIPALNAME do AD local termina com a parte de domínio <em>@partners.fabrikam123.org</em>. 

    Conforme mencionado anteriormente, Azure AD Connect não permite que o atributo UserType nos usuários existentes do Azure AD seja alterado por Azure AD Connect. Portanto, você deve garantir que a lógica que você decidiu é consistente com o modo como o atributo UserType já está configurado para todos os usuários existentes do Azure AD em seu locatário.

As etapas para habilitar a sincronização do atributo UserType podem ser resumidas como:

1.  Desabilite o Agendador de sincronização e verifique se não há nenhuma sincronização em andamento.
2.  Adicione o atributo de origem ao esquema do AD Connector local.
3.  Adicione o UserType ao esquema do Azure AD Connector.
4.  Crie uma regra de sincronização de entrada para fluir o valor do atributo do Active Directory local.
5.  Crie uma regra de sincronização de saída para fluir o valor do atributo para o Azure AD.
6.  Executar um ciclo de sincronização completo.
7.  Habilite o Agendador de sincronização.

>[!NOTE]
> O restante desta seção aborda essas etapas. Elas são descritas no contexto de uma implantação do Azure AD com topologia de floresta única e sem regras de sincronização personalizadas. Se você tiver uma topologia de várias florestas, regras de sincronização personalizadas configuradas ou tiver um servidor de preparo, será necessário ajustar as etapas adequadamente.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Etapa 1: desabilitar o Agendador de sincronização e verificar se não há nenhuma sincronização em andamento
Para evitar a exportação de alterações não intencionais para o Azure AD, certifique-se de que nenhuma sincronização ocorra enquanto você estiver no meio da atualização de regras de sincronização. Para desabilitar o Agendador de sincronização interno:

 1. Inicie uma sessão do PowerShell no servidor de Azure AD Connect.
 2. Desabilite a sincronização agendada executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Abra o Synchronization Service Manager acessando **iniciar** > **serviço de sincronização**.
 4. Vá para a guia **operações** e confirme se não há nenhuma operação com o status *em andamento*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Etapa 2: Adicionar o atributo de origem ao esquema do AD Connector local
Nem todos os atributos do Azure AD são importados para o espaço do AD Connector local. Para adicionar o atributo de origem à lista dos atributos importados:

 1. Vá para a guia **conectores** na Synchronization Service Manager.
 2. Clique com o botão direito do mouse no AD Connector local e selecione **Propriedades**.
 3. Na caixa de diálogo pop-up, vá para a guia **Selecionar atributos** .
 4. Verifique se o atributo de origem está marcado na lista de atributos.
 5. Clique em **OK** para salvar.
![adicionar o atributo de origem ao esquema do AD Connector local](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Etapa 3: Adicionar o UserType ao esquema do Azure AD Connector
Por padrão, o atributo UserType não é importado para o espaço de Azure AD Connect. Para adicionar o atributo UserType à lista de atributos importados:

 1. Vá para a guia **conectores** na Synchronization Service Manager.
 2. Clique com o botão direito do mouse no **Azure ad Connector** e selecione **Propriedades**.
 3. Na caixa de diálogo pop-up, vá para a guia **Selecionar atributos** .
 4. Verifique se o atributo UserType está marcado na lista de atributos.
 5. Clique em **OK** para salvar.

![Adicionar atributo de origem ao esquema do Azure AD Connector](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Etapa 4: criar uma regra de sincronização de entrada para fluir o valor do atributo do local Active Directory
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem do Active Directory local para o metaverso:

1. Abra o editor de regras de sincronização acessando **iniciar** > **Editor de regras de sincronização**.
2. Defina a **direção** do filtro de pesquisa como **entrada**.
3. Clique no botão **Adicionar nova regra** para criar uma nova regra de entrada.
4. Na guia **Descrição** , forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Forneça um nome* | Por exemplo, *entrada do AD – UserType do usuário* |
    | Descrição | *Forneça uma descrição* |  |
    | Sistema conectado | *Selecione o AD Connector local* |  |
    | Tipo de objeto do sistema conectado | **Usuário** |  |
    | Tipo de objeto do metaverso | **Pessoa** |  |
    | Tipo de link | **Associar** |  |
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 é reservado para regras de sincronização personalizadas. Não escolha um valor que seja usado por outra regra de sincronização. |

5. Vá para a guia **filtro de escopo** e adicione um **único grupo de filtro de escopo** com a seguinte cláusula:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | \_ do usuário |

    O filtro de escopo determina a quais objetos do AD local essa regra de sincronização de entrada é aplicada. Neste exemplo, usamos o mesmo filtro de escopo usado na regra de sincronização pronta para uso *do AD – usuário comum* , que impede que a regra de sincronização seja aplicada a objetos de usuário criados por meio do recurso de write-back de usuário do Azure AD. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação de Azure AD Connect.

6. Vá para a guia **transformação** e implemente a regra de transformação desejada. Por exemplo, se você designou um atributo do AD local não utilizado (como extensionAttribute1) como o atributo de origem para o UserType, você pode implementar um fluxo de atributos direto:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Direct | userType | extensionAttribute1 | desmarcada | Atualizar |

    Em outro exemplo, você deseja derivar o valor para o atributo UserType de outras propriedades. Por exemplo, você deseja sincronizar todos os usuários como convidados se seu atributo userPrincipalName do AD local termina com a parte de domínio <em>@partners.fabrikam123.org</em>. Você pode implementar uma expressão como esta:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Expressão | userType | IIF (IsPresent ([userPrincipalName]), IIF (CBool (Instr (LCase ([userPrincipalName]), "@partners.fabrikam123.org") = 0), "membro", "convidado"), erro ("UserPrincipalName não está presente para determinar UserType")) | desmarcada | Atualizar |

7. Clique em **Adicionar** para criar a regra de entrada.

![Criar regra de sincronização de entrada](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Etapa 5: criar uma regra de sincronização de saída para fluir o valor do atributo para o Azure AD
A regra de sincronização de saída permite que o valor do atributo flua do metaverso para o atributo UserType no Azure AD:

1. Vá para o editor de regras de sincronização.
2. Defina a **direção** do filtro de pesquisa como de **saída**.
3. Clique no botão **Adicionar nova regra** .
4. Na guia **Descrição** , forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | Nome | *Forneça um nome* | Por exemplo, *out to AAD – UserType do usuário* |
    | Descrição | *Forneça uma descrição* ||
    | Sistema conectado | *Selecione o conector do AAD* ||
    | Tipo de objeto do sistema conectado | **Usuário** ||
    | Tipo de objeto do metaverso | **Pessoa** ||
    | Tipo de link | **Associar** ||
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 é reservado para regras de sincronização personalizadas. Não escolha um valor que seja usado por outra regra de sincronização. |

5. Vá para a guia **filtro de escopo** e adicione um **único grupo de filtro de escopo** com duas cláusulas:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilizador |
    | cloudMastered | NOTEQUAL | Verdadeiro |

    O filtro de escopo determina a quais objetos do Azure AD essa regra de sincronização de saída é aplicada. Neste exemplo, usamos o mesmo filtro de escopo da regra de sincronização pronto *para AD – identidade de usuário pronta para* uso. Ele impede que a regra de sincronização seja aplicada a objetos de usuário que não estão sincronizados do Active Directory local. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação de Azure AD Connect.

6. Vá para a guia **transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Direct | userType | userType | desmarcada | Atualizar |

7. Clique em **Adicionar** para criar a regra de saída.

![Criar regra de sincronização de saída](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Etapa 6: executar um ciclo de sincronização completo
Em geral, um ciclo de sincronização completo é necessário porque adicionamos novos atributos aos esquemas de conectores Active Directory e AD do Azure e introduzimos regras de sincronização personalizadas. Você deseja verificar as alterações antes de exportá-las para o Azure AD. 

Você pode usar as etapas a seguir para verificar as alterações enquanto executa manualmente as etapas que compõem um ciclo de sincronização completo.

1. Execute uma **importação completa** no **ad Connector local**:

   1. Vá para a guia **operações** no Synchronization Service Manager.
   2. Clique com o botão direito do mouse no **ad Connector local** e selecione **executar**.
   3. Na caixa de diálogo pop-up, selecione **importação completa** e clique em **OK**.
   4. Aguarde a conclusão da operação.

      > [!NOTE]
      > Você pode ignorar uma importação completa no AD Connector local se o atributo de origem já estiver incluído na lista de atributos importados. Em outras palavras, você não precisa fazer nenhuma alteração durante a [etapa 2: Adicionar o atributo de origem ao esquema do AD Connector local](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Execute uma **importação completa** no **Azure ad Connector**:

   1. Clique com o botão direito do mouse no **Azure ad Connector** e selecione **executar**.
   2. Na caixa de diálogo pop-up, selecione **importação completa** e clique em **OK**.
   3. Aguarde a conclusão da operação.

3. Verifique se a regra de sincronização é alterada em um objeto de usuário existente:

    O atributo de origem do Active Directory local e o UserType do Azure AD foram importados em seus respectivos espaços do conector. Antes de prosseguir com uma sincronização completa, faça uma **Visualização** em um objeto de usuário existente no espaço do AD Connector local. O objeto escolhido deve ter o atributo de origem preenchido.
    
    Uma **Visualização** bem-sucedida com o UserType populado no metaverso é um bom indicador de que você configurou as regras de sincronização corretamente. Para obter informações sobre como fazer uma **Visualização**, consulte a seção [verificar a alteração](#verify-the-change).

4. Execute uma **sincronização completa** no **ad Connector local**:

   1. Clique com o botão direito do mouse no **ad Connector local** e selecione **executar**.
   2. Na caixa de diálogo pop-up, selecione **sincronização completa** e clique em **OK**.
   3. Aguarde a conclusão da operação.

5. Verificar **exportações pendentes** para o Azure AD:

   1. Clique com o botão direito do mouse no **conector do Azure ad** e selecione **Pesquisar espaço do conector**.
   2. Na caixa de diálogo pop-up **Pesquisar espaço conector** :

      - Defina o **escopo** como **exportação pendente**.
      - Marque todas as três caixas de seleção: **Adicionar**, **Modificar**e **excluir**.
      - Clique no botão **Pesquisar** para obter a lista de objetos com as alterações a serem exportadas. Para examinar as alterações de um determinado objeto, clique duas vezes no objeto.
      - Verifique se as alterações são esperadas.

6. Execute **Exportar** no **Azure ad Connector**:

   1. Clique com o botão direito do mouse no **Azure ad Connector** e selecione **executar**.
   2. Na caixa de diálogo pop-up **executar conector** , selecione **Exportar** e clique em **OK**.
   3. Aguarde até que a exportação para o Azure AD seja concluída.

> [!NOTE]
> Essas etapas não incluem a sincronização completa e as etapas de exportação no conector do AD do Azure. Essas etapas não são necessárias porque os valores de atributo estão fluindo do Active Directory local para o Azure AD apenas.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Etapa 7: reabilitar o Agendador de sincronização
Reabilitar o Agendador de sincronização interno:

1. Inicie uma sessão do PowerShell.
2. Habilite novamente a sincronização agendada executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração em [noções básicas sobre provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão em [noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Tópicos de visão geral**

* [Sincronização de Azure AD Connect: entender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
